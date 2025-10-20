#  Relatório Casos Práticos e Simulações - Nível 10.

##  Descrição

Este relatório apresenta as ações práticas e simulações estratégicas da empresa **Momento**, incluindo expansão internacional, análise de custos, auditoria de vendas e um dashboard executivo solicitado pelo CEO.

##  Metodologia

Foram realizadas inserções, consultas e agregações no MongoDB para: criar novos escritórios e departamentos, contratar funcionários, identificar custos e irregularidades em vendas, e gerar um resumo executivo consolidado. As queries foram estruturadas para facilitar futuras simulações e ajustes.

---

##  Informações

### 10.1 Criação do escritório "Momento Brasil"

```javascript
db.escritorios.insertOne({
  _id: ObjectId("5f8b3f3f9b3e0b3b3c1e3e42"),
  nome: "Momento Brasil",
  endereco: "Avenida Brigadeiro Faria Lima, 4500, Itaim Bibi, São Paulo - SP, 04538-132",
  telefone: "+55 (11) 3055-2025",
  pais: "Brasil",
  tarefa_a_fazer: [
    {
      nome_tarefa: "Organização de Arquivos",
      descricao: "Implementação de sistema de arquivamento físico e digital para documentos internos.",
      status: "Em andamento"
    },
    {
      nome_tarefa: "Agenda Corporativa",
      descricao:"Planejamento e gestão de agendas de executivos e reuniões estratégicas.",
      status: "Em andamento"
    },
    {
      nome_tarefa: "Comunicação Interna",
      descricao: "Melhoria do fluxo de comunicação entre setores e atualização de protocolos internos.",
      status: "Em andamento"
    }
  ],
  suprimentos: [
    { produto: "Computador Desktop", quantidade: 6, precoUnitario: 4000.00  },
    { produto: "Impressora Multifuncional", quantidade: 2, precoUnitario:  1200.00  },
    { produto: "Telefone Corporativo", quantidade: 6, precoUnitario: 350.00  },
    { produto: "Scanner de Documentos", quantidade: 3, precoUnitario: 9500.50 },
    { produto: "Cafeteira Elétrica", quantidade: 1, precoUnitario:  300.00 }
  ]
});
```

**Resultado:**

*O escritorio "Momento Brasil" foi criado e abastecido com sucesso.*

### 10.2 Criação do departamento "Operações LATAM"

```javascript
db.departamentos.insertOne({
  nome: "Operações LATAM",
  escritorio: ObjectId("5f8b3f3f9b3e0b3b3c1e3e42")
});
```

**Resultado:**

*O departamento "Operaões Latam" foi criado com sucesso.*

### 10.3 Contratação de 5 novos funcionários

```javascript
db.funcionarios.insertMany([
  {
    "nome": "Vicky Maestre",
    "telefone": "+55 (11) 98877-1001",
    "email": "v.maestre@momento.org",
    "dataAdmissao": "2025-10-10",
    "cargo": "Coordenadora de Secretariado",
    "salario":  12000.00,
    "departamento": ObjectId("68f5b1cf5314f19b00962e80")
  },
  {
    "nome": "Rayssa da Cruz",
    "telefone": "+55 (16) 91792-2045",
    "email": "ray.cruz.@momento.org",
    "dataAdmissao": "2025-10-10",
    "cargo": "Diretora Administrativa",
    "salario":  18000.00,
    "departamento": ObjectId("68f5b1cf5314f19b00962e80")
  },
  {
    "nome": "Jão Marciel",
    "telefone": "+55 (11) 99234-4850",
    "email": "J.Marciel@momento.org",
    "dataAdmissao": "2025-10-10",
    "cargo": "Assistente Administrativo Sênior",
    "salario":  11000.00,
    "departamento": ObjectId("68f5b1cf5314f19b00962e80")
  },
  {
    "nome": "Beatriz Cruz",
    "telefone": "+55 (11) 98267-2007",
    "email": "b.cruz@momento.org",
    "dataAdmissao": "2022-05-02",
    "cargo": "Assistente de Secretariado",
    "salario": 9000.00,
    "departamento": ObjectId("68f5b1cf5314f19b00962e80")
  },
  {
    "nome": "Laura Belen",
    "telefone": "+55 (11) 98297-2025",
    "email": "l.Belen@momento.org",
    "dataAdmissao": "2025-10-10",
    "cargo": "Gerente de Operações Administrativas",
    "salario": 13000.00,
    "departamento": ObjectId("68f5b1cf5314f19b00962e80")
  },
])
```

**Resultado simulado:**
 
 *Cinco novos funcionários tem començado na Momento Brasil*

### 10.4 Identificação dos 3 escritórios com maiores custos de suprimentos

```javascript
db.escritorios.aggregate([
  { $addFields: { custoTotalSuprimentos: { $sum: { $map: { input: "$suprimentos", as: "s", in: { $multiply: ["$$s.quantidade", "$$s.precoUnitario"] } } } } } },
  { $sort: { custoTotalSuprimentos: -1 } },
  { $limit: 3 }
]);
```

**Resultado:**

*Wayne Offices, Stark Industries, Umbrella Corp são  escritórios com maiores custos de suprimentos.*

### 10.5 Sugestão de corte de custos (suprimentos com quantidade > 50)

```javascript
db.escritorios.aggregate([
  { $unwind: "$suprimentos" }, 
  { $match: { "suprimentos.quantidade": { $gt: 50 } } },
  { 
    $project: { 
      escritorio: "$nome", 
      item: "$suprimentos.produto", 
      quantidade: "$suprimentos.quantidade", 
      valorUnitario: "$equipamentos_laboratorio.precoUnitario" 
    } 
  }
])
```

**Resultado:**

  * item: 'Luvas de Ferro (unitário)',
  quantidade: 143
  * item: 'Folhas de Sulfito',
  quantidade: 500000

### 10.6 Cálculo de economia com redução de 20% nos salários acima de $15.000

**Cálculo:**


*Salários acima de $15.000: $120.000
Economia 20%: $24.000*

### 10.7 Vendas sem vendedor associado

```javascript
db.vendas.find({ vendedor: { $exists: false } });
```

**Resultado:**

```
 _id: ObjectId('5f8b3f3f9b3e0b3b3c1e3e49'),
  produto: 'Lança-Teias',
  quantidade: 3,
  precoUnitario: 237.19,
  dataVenda: '2023-07-13']
```

### 10.8 Vendedores não registrados na coleção de funcionários

```javascript
db.vendas.aggregate([
  { $lookup: { from: 'funcionarios', localField: 'vendedor', foreignField: '_id', as: 'func' } },
  { $match: { func: { $size: 0 } } }
]);
```

**Resultado:**

```
 _id: ObjectId('5f8b3f3f9b3e0b3b3c1e3e49'),
  produto: 'Lança-Teias',
  quantidade: 3,
  precoUnitario: 237.19,
  dataVenda: '2023-07-13',
  Vendedor: []
```

### 10.9 Produtos com variação > 10% no preço unitário

```javascript
db.vendas.aggregate([
  { $group: { _id: '$produto', minPreco: { $min: '$precoUnitario' }, maxPreco: { $max: '$precoUnitario' } } },
  { $addFields: { variacao: { $divide: [ { $subtract: ['$maxPreco', '$minPreco'] }, '$minPreco' ] } } },
  { $match: { variacao: { $gt: 0.1 } } }
]);
```

**Resultado**

*Não possuimos nenhum produto que cumpla com esses requisitos*

### 10.10 Dashboard executivo consolidado

```javascript
db.funcionarios.aggregate([
  {
    // Total de funcionários
    $group: {
      _id: null,
      totalFuncionarios: { $sum: 1 },
      custoTotalSalarios: { $sum: "$salario" }
    }
  },
  {
    $lookup: {
      from: "departamentos",
      pipeline: [
        { $count: "totalDepartamentos" }
      ],
      as: "departamentos"
    }
  },
  {
    $lookup: {
      from: "escritorios",
      pipeline: [
        { $count: "totalEscritorios" }
      ],
      as: "escritorios"
    }
  },
  {
    $lookup: {
      from: "vendas",
      pipeline: [
        {
          $group: {
            _id: "$produto",
            receitaProduto: { $sum: { $multiply: ["$quantidade", "$precoUnitario"] } }
          }
        },
        { $sort: { receitaProduto: -1 } },
        { $limit: 1 }
      ],
      as: "produtoMaisVendido"
    }
  },
  {
    $lookup: {
      from: "vendas",
      pipeline: [
        {
          $group: {
            _id: null,
            receitaTotalVendas: { $sum: { $multiply: ["$quantidade", "$precoUnitario"] } }
          }
        }
      ],
      as: "receitaTotalVendas"
    }
  },
  {
    $project: {
      _id: 0,
      totalFuncionarios: 1,
      custoTotalSalarios: 1,
      totalDepartamentos: { $arrayElemAt: ["$departamentos.totalDepartamentos", 0] },
      totalEscritorios: { $arrayElemAt: ["$escritorios.totalEscritorios", 0] },
      receitaTotalVendas: { $arrayElemAt: ["$receitaTotalVendas.receitaTotalVendas", 0] },
      produtoMaisVendido: { $arrayElemAt: ["$produtoMaisVendido._id", 0] }
    }
  }
]);
```

**Resultado simulado:**

```
Dashboard consolidado pronto.

 * Total de Funcionarios: 36,
 * Custo total de salarios: 429546,
 * Total de departamentos: 9,
 * Total de escritorios: 5,
 * Receita total de vendas: 27076.15,
 * Produto mais vendido: 'Sabre de Luz (Mace Windu)'
```

---

## 🧠 Conclusões Alcançadas

* A criação do escritório "Momento Brasil" fortalece a presença internacional da empresa, oferecendo infraestrutura moderna e projetos estratégicos de P&D.
* O novo departamento "Operações LATAM" permite centralizar a gestão regional e facilitar a expansão de funcionários e processos.
* A análise de custos indica quais escritórios e suprimentos demandam atenção para otimização financeira.
* As vendas com inconsistências ou produtos com variação de preço alertam para a necessidade de auditoria contínua.
* O dashboard executivo fornece uma visão consolidada da empresa, permitindo decisões estratégicas rápidas e fundamentadas.
