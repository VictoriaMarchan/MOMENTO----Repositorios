# Relatório de Análise Financeira Básica – Nível 2

##  Descrição

Este relatório apresenta uma visão financeira consolidada da empresa **Momento**, analisando o número de funcionários por departamento, o custo total de salários, médias salariais e comparativos entre setores. O objetivo é fornecer ao CFO informações claras sobre **custos operacionais e distribuição salarial** dentro da empresa.

##  Metodologia

Para este relatório, foram utilizadas agregações no MongoDB com os operadores `$match`, `$group`, `$avg` e `$sum` a fim de calcular o salário total e a média por departamento, além de identificar os setores com maior e menor custo. A metodologia aplicada permitiu consolidar informações dispersas da coleção **funcionarios**, fornecendo uma visão clara do impacto financeiro e facilitando as tomadas de decisão estratégicas.

---

## Informações

### Consultas Utilizadas

```javascript
// 2.1 Quantos funcionários trabalham no Departamento de Vendas?
db.funcionarios.countDocuments({ departamento: ObjectId("85992103f9b3e0b3b3c1fe71") });

// 2.2 Qual é o custo total com salários do Departamento de Vendas?
db.funcionarios.aggregate([
  { $match: { departamento:  ObjectId("85992103f9b3e0b3b3c1fe71") } },
  { $group: { _id: null, custoTotal: { $sum: "$salario" } } }
]);

// 2.3 Qual é a média salarial da empresa, excluindo os cargos de CEO, CMO e CFO?
db.funcionarios.aggregate([
  { $match: { cargo: { $nin: ["CEO", "CFO", "CMO"] } } },
  { $group: { _id: null, mediaSalarial: { $avg: "$salario" } } }
]);

// 2.4 Qual é a média salarial do Departamento de Tecnologia?
db.funcionarios.aggregate([
  { $match: { departamento: "Tecnologia" } },
  { $group: { _id: "$departamento", media: { $avg: "$salario" } } }
]);

// 2.5 Qual departamento possui a maior média salarial?
db.funcionarios.aggregate([
  { $group: { _id: "$departamento", media: { $avg: "$salario" } } },
  { $sort: { media: -1 } },
  { $limit: 1 }
]);

// 2.6 Qual departamento possui o menor número de funcionários?
db.funcionarios.aggregate([
  { $group: { _id: "$departamento", total: { $sum: 1 } } },
  { $sort: { total: 1 } },
  { $limit: 1 }
]);
```

### ⚠ Ajustes / Observações

Durante o desenvolvimento, foi necessário ajustar a consulta do **Departamento de Tecnologia**, substituindo o uso do `ObjectId` por uma correspondência direta do nome, pois os registros estavam em formato de string. Sem essa correção, o comando retornava vazio.

---

###  Resultados Obtidos

| Nº  | Consulta                                         | Resultado                     |
| --- | ------------------------------------------------ | ----------------------------- |
| 2.1 | Funcionários no Departamento de Vendas           | **10 funcionários**           |
| 2.2 | Custo total com salários (Vendas)                | **$95.100**                   |
| 2.3 | Média salarial da empresa (excluindo executivos) | **$9.551,30**                 |
| 2.4 | Média salarial do Departamento de Tecnologia     | **$4.633,34**                 |
| 2.5 | Departamento com maior média salarial            | **Financeiro ($71.000)**      |
| 2.6 | Departamento com menor número de funcionários    | **Executivo (1 colaborador)** |

---

##  Conclusões Alcançadas

* O **Departamento de Vendas** representa um custo relevante na folha, proporcional ao seu tamanho e importância estratégica.
* O **setor de Tecnologia** possui uma média salarial **superior à média da empresa**, refletindo o investimento em talentos técnicos.
* O **departamento Financeiro** lidera em média salarial devido à concentração de cargos executivos.
* O **departamento Executivo**, com apenas um colaborador, é o menor em tamanho, mas de alta representatividade.
* A análise revela uma **estrutura de custos equilibrada**, com crescimento previsto em áreas tecnológicas e inovação.
* Recomenda-se o **monitoramento trimestral da folha salarial** para assegurar controle financeiro e retenção de talentos.
