# ️ Relatório de Operações de Atualização – Nível 6

##  Descrição

Este relatório trata de **atualizações estratégicas na estrutura da empresa**, incluindo criação de departamentos, movimentação de funcionários, ajustes salariais, promoções e gerenciamento de suprimentos. O objetivo é **refletir o crescimento da empresa e manter o banco de dados atualizado**.

##  Metodologia

As queries foram utilizadas para atualizar o banco de forma rápida e segura:

* Criação do departamento "Inovações" com `insertOne`
* Transferência de 2 funcionários de Tecnologia para Inovações com `updateMany`
* Aumento de 10% nos salários do departamento de Tecnologia com `$mul`
* Promoção de Bruce Ernst com `updateOne`
* Inclusão de Headsets no escritório Wayne Offices com `$push`
* Remoção de funcionários contratados antes de 1990 com `deleteMany`, após conferência com `find()`

---

##  Informações

###  Consultas Utilizadas

```javascript
// 6.1 Criar novo departamento "Inovações" no escritório "Wayne Offices"
db.departamentos.insertOne({
  nome: "Inovações",
  escritorio: ObjectId("5f8b3f3f9b3e0b3b3c1e3e3e")
});

// 6.2 Transferir 2 funcionários de Tecnologia para Inovações
db.funcionarios.updateOne(
  { nome: "Diana Lorentz" },
  {
    $set: {
      departamento: ObjectId("68efd0b5a8a7bd89c14eb64f"),
      cargo: "Diretora de Inovação",
      salario: 20434
    }
  }
);

db.funcionarios.updateOne(
  { nome: "Valli Stark" },
  {
    $set: {
      departamento: ObjectId("68efd0b5a8a7bd89c14eb64f"),
      cargo: "Líder de Projeto de IA",
      salario: 19532
    }
  }
);

// 6.3 Aumentar 10% dos salários do departamento de Tecnologia
db.funcionarios.updateMany(
  { departamento: ObjectId("85992103f9b3e0b3b3c1fe74") },
  { $mul: { salario: 1.1 } }
);

// 6.4 Promover Bruce Ernst
db.funcionarios.updateOne(
  { nome: "Bruce Ernst" },
  { $set: { cargo: "Senior Web Developer", salario: 5000 } }
);

// 6.5 Adicionar novo suprimento "Headsets" no escritório Wayne Offices
db.escritorios.updateOne(
  { nome: "Wayne Offices" },
  { $push: { suprimentos: { produto: "Headsets", quantidade: 15, precoUnitario: 150 } } }
);

// 6.6 Remover funcionários contratados antes de 1990
db.funcionarios.find({ dataAdmissao: { $lt: ISODate("1990-01-01") } });
db.funcionarios.deleteMany({ dataAdmissao: { $lt: ISODate("1990-01-01") } });
```

###  Ajustes / Observações

* Foi necessário conferir os funcionários antes do `deleteMany` para evitar exclusões indevidas.
* O ID do departamento Inovações precisou ser definido manualmente na transferência de funcionários.
* As datas de admissão foram verificadas para evitar inconsistências de formatação.

### Resultados Obtidos

| Nº  | Ação                          | Resultado                                           |
| --- | ----------------------------- | --------------------------------------------------- |
| 6.1 | Novo departamento criado      | **Inovações** no Wayne Offices, 0 funcionários      |
| 6.2 | Transferência de funcionários | 2 funcionários de Tecnologia movidos para Inovações |
| 6.3 | Aumento salarial              | Funcionários de Tecnologia receberam **+10%**       |
| 6.4 | Promoção individual           | Bruce Ernst: **Senior Web Developer**, $5.000       |
| 6.5 | Novo suprimento               | Headsets adicionados: 15 unidades, $150 cada        |
| 6.6 | Aposentadoria                 | Funcionários contratados antes de 1990 removidos    |

---

## Conclusões Alcançadas

* Criação do departamento **Inovações** fortalece a área de pesquisa e desenvolvimento, refletindo crescimento estratégico.
* Transferência dos funcionários Valli Stark e Diana Lorentz garante que **talentos sejam alocados onde mais necessários**.
* Ajustes salariais e promoções demonstram políticas de **retenção e motivação de funcionários**.
* Inclusão de suprimentos garante que o escritório tenha **equipamentos atualizados para novas operações**.
* Remoção de funcionários antigos mantém o banco de dados **limpo e alinhado**.
