#  Relatório de Otimização – Nível 8

##  Descrição

Este relatório aborda consultas de **otimização e análise avançada** no banco de dados da empresa **Momento**, explorando desafios de desempenho, lógica, agregação e busca textual. O objetivo é aplicar técnicas de **agregação, lookup, text search e manipulação de datas** para extrair insights complexos e identificar inconsistências ou padrões relevantes.

## Metodologia

Foram utilizadas funções de agregacão, consultas com operadores condicionais e buscas textuais para realizar análises mais complexas e identificar relações entre coleções. As queries foram testadas quanto à performance e legibilidade, priorizando soluções que pudessem ser reusadas em relatórios futuros.

---

## ️ Informações

###  Consultas Utilizadas

#### 8.1 – Funcionários do Departamento de Vendas com Dependentes

```javascript
db.funcionarios.find({
  departamento: ObjectId("85992103f9b3e0b3b3c1fe71"),
  $or: [
    { "dependentes.conjuge": { $exists: true, $ne: null } },
    { "dependentes.filhos.0": { $exists: true } }
  ]
});

db.funcionarios.aggregate([
  { $match: { departamento: ObjectId("85992103f9b3e0b3b3c1fe71") } },
  { $addFields: {
      filhosArray: { $ifNull: ["$dependentes.filhos", []] },
      temConjuge: { $cond: [{ $gt: [{ $ifNull: [ "$dependentes.conjuge", null ] }, null ] }, true, false] }
    }
  },
  { $addFields: { temFilhos: { $gt: [ { $size: "$filhosArray" }, 0 ] } } },
  { $match: { $or: [{ temConjuge: true }, { temFilhos: true }] } },
  { $project: { nome: 1, cargo: 1, salario: 1, temConjuge: 1, temFilhos: 1 } }
]);

db.funcionarios.find({
  $expr: {
    $and: [
      { $eq: ["$departamento", ObjectId("85992103f9b3e0b3b3c1fe71")] },
      { $or: [
          { $gt: [ { $size: { $ifNull: ["$dependentes.filhos", []] } }, 0 ] },
          { $ne: [ { $ifNull: ["$dependentes.conjuge", null] }, null ] }
        ]
      }
    ]
  }
});
```

**Resultados:**

> *Pat Ferreira (2 filhos e sua esposa) 
   Sarah Bell ( um filho e seu esposo)*

---

#### 8.2 – Funcionários com Escritórios Diferentes de Seus Departamentos

```javascript
//verificar inconsistencias
db.funcionarios.aggregate([
  {
    $lookup: {
      from: "departamentos",
      localField: "departamento",
      foreignField: "_id",
      as: "infoDept"
    }
  },
  { $unwind: "$infoDept" },
  {
    $match: {
      $expr: { $ne: ["$escritorio", "$infoDept.escritorio"] }
    }
  },
  { $project: { nome: 1, escritorio: 1, departamento: 1, escritorioDept: "$infoDept.escritorio" } }
]);

// corrigir inconsistencias 
const cursor = db.funcionarios.aggregate([
  {
    $lookup: {
      from: "departamentos",
      localField: "departamento",
      foreignField: "_id",
      as: "infoDept"
    }
  },
  { $unwind: "$infoDept" },
  {
    $match: {
      $expr: { $ne: ["$escritorio", "$infoDept.escritorio"] }
    }
  },
  {
    $project: {
      _id: 1,
      nome: 1,
      escritorioAtual: "$escritorio",
      escritorioCorreto: "$infoDept.escritorio"
    }
  }
]);
cursor.forEach(doc => {
  print("Corrigindo:", doc._id.valueOf(), doc.nome, "de", doc.escritorioAtual, "→", doc.escritorioCorreto);
  db.funcionarios.updateOne(
    { _id: doc._id },
    { $set: { escritorio: doc.escritorioCorreto } }
  );
});

```

**Resultados:**

> *Todos os funcionários foram transferidos aos seus escritorios correspondentes.*

---

#### 8.3 – Relatório Completo de um Escritório

```javascript
db.escritorios.aggregate([
  { $match: { nome: "Wayne Offices" } },
  {
    $lookup: {
      from: "departamentos",
      localField: "_id",
      foreignField: "escritorio",
      as: "departamentos"
    }
  },
  {
    $lookup: {
      from: "funcionarios",
      localField: "departamentos._id",
      foreignField: "departamento",
      as: "funcionarios"
    }
  },
  {
    $lookup: {
      from: "suprimentos",
      localField: "_id",
      foreignField: "escritorio",
      as: "suprimentos"
    }
  },
  {
    $addFields: {
      numeroDepartamentos: { $size: "$departamentos" },
      totalFuncionarios: { $size: "$funcionarios" },
      custoSalarios: { $sum: "$funcionarios.salario" },
      custoSuprimentos: { $sum: { $map: { input: "$suprimentos", as: "s", in: { $multiply: ["$$s.quantidade", "$$s.precoUnitario"] } } } }
    }
  },
  {
    $project: {
      nome: 1,
      pais: 1,
      numeroDepartamentos: 1,
      totalFuncionarios: 1,
      custoSalarios: 1,
      custoSuprimentos: 1
    }
  }
]);
```

**Resultados:**

> *nome: 'Wayne Offices',
  pais: 'USA',
  numeroDepartamentos: 5,
  totalFuncionarios: 16,
  custoSalarios: 228746,
  custoSuprimentos: 0*

---

#### 8.4 – Departamento Mais Equilibrado (Menor Diferença Salarial)

```javascript
db.funcionarios.aggregate([
  {
    $group: {
      _id: "$departamento",
      salarioMax: { $max: "$salario" },
      salarioMin: { $min: "$salario" }
    }
  },
  { $addFields: { diferenca: { $subtract: ["$salarioMax", "$salarioMin"] } } },
  { $sort: { diferenca: 1 } },
  { $limit: 1 }
]);
```

**Resultados:**

> *_id: ObjectId('85992103f9b3e0b3b3c1fe70'),
  salarioMax: 71000,
  salarioMin: 71000,
  diferenca: 0*

---

#### 8.5 – Busca Textual por Produtos "Uniforme"

```javascript
db.vendas.find({ produto: /Uniforme/i })
```

**Resultados:**

> *'Uniforme do Superman', 'Uniforme de Moléculas Instáveis'*

---

#### 8.6 – Vendas do Segundo Trimestre de 2023

```javascript
db.vendas.aggregate([
  {
    $addFields: {
      dataVendaISO: {
        $cond: [
          { $eq: [{ $type: "$dataVenda" }, "string"] },
          { $toDate: "$dataVenda" },
          "$dataVenda"
        ]
      }
    }
  },
  {
    $match: {
      dataVendaISO: {
        $gte: ISODate("2023-04-01T00:00:00Z"),
        $lte: ISODate("2023-06-30T23:59:59Z")
      }
    }
  },
  {
    $project: {
      produto: 1,
      quantidade: 1,
      precoUnitario: 1,
      vendedor: 1,
      dataVendaISO: 1
    }
  }
]);
```

**Resultados:**

 * Produto: 'Uniforme do Superman'.
  Quantidade: 1.
  Preço unitario: 300.13.
  Vendedor: Jenny Tseng.
  Data da Venda: 2023-06-19.                        

  * Produto: 'Lança-Teias'.
  Quantidade: 3.
  Preço unitario: 237.19.
  vendedor: Normam Osborn.
  data da venda: 2023-06-05.
  * Produto: 'Capacete do Homem-Formiga'.
  Quantidade: 4.
  Preço unitario: 289.29.
  Vendedor: Jon Deegan.
  Data da venda: 2023-06-21.
  *  Produto: 'Nulificador Total'.
  Quantidade: 5.
  Preço unitario: 750.19.
  Vendedor: Michael Hartstein,
  Data da venda: 2023-06-13.
* Produto: 'Laço da Verdade'.
  Quantidade: 6.
  Preço unitario: 325.13.
  Vendedor: Sundar Ande.
  Data da venda: 2023-06-14.
* Produto: 'Laço da Verdade',
  Quantidade: 6.
  Preço unitario: 325.13.
  Vendedor: Jon Deegan.
  Data da venda: 2023-06-10.
* Produto: 'Sabre de Luz (Mace Windu)'.
  Quantidade: 8.
  Preço unitario: 990.29.
  Vendedor: Michael Hartstein.
  Data da Venda: 2023-06-15
* Produto: 'Sentinelas do Bolivar Trask'.
  Quantidade: 9.
  Preço unitario: 150.13.
  Vendedor: Jon Deegan.
  Data da venda: 2023-06-20.
* Produto: 'Uniforme de Moléculas Instáveis'.
  Quantidade: 10.
  Preço unitario: 158.29.
  Vendedor: "Michael Hartstein.
  Data da venda: 2023-06-29.
---

###  Ajustes / Observações


* Em 8.3, foi necessário garantir que o **campo de junção** (`escritorio`) estivesse correto em todas as coleções.
* A consulta 8.6 incluiu uma conversão para `ISODate`, evitando falhas na comparação de datas armazenadas como string.

---

##  Conclusões Alcançadas

* As otimizações implementadas melhoraram a confiabilidade e reduziram falhas lógicas nos resultados, preparando o ambiente para relatórios corporativos mais robustos nos próximos meses.*
