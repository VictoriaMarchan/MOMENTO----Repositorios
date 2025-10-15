#  Relatório de Produtos e Vendas – Nível 5

##  Descrição

Este relatório analisa as vendas da empresa **Momento**, identificando **produtos vendidos, receitas geradas e desempenho de vendedores**. O objetivo é fornecer insights para **estratégias comerciais e planejamento de estoque**.

##  Metodologia

Foram utilizadas agregações no MongoDB com os operadores `$group`, `$sum`, `$max` e `$sort` para identificar os produtos mais vendidos, o faturamento total e o desempenho dos vendedores. Essa abordagem consolida dados de vendas dispersos e permite análise de faturamento, desempenho individual e popularidade dos produtos, proporcionando insights estratégicos.

---

##  Informações

###  Consultas Utilizadas

```javascript
// 5.1 Produtos vendidos (únicos)
db.vendas.distinct("produto");

// 5.2 Produto mais vendido
 db.vendas.aggregate([
  { $group: { _id: "$produto", totalQtd: { $sum: "$quantidade" } } },
  { $sort: { totalQtd: -1 } },
  { $limit: 1 }
]);

// 5.3 Produto menos vendido
 db.vendas.aggregate([
  { $group: { _id: "$produto", totalQuantidade: { $sum: "$quantidade" } } },
  { $sort: { totalQuantidade: 1 } },
  { $limit: 1 }
]);

// 5.4 Produto que gerou mais receita
 db.vendas.aggregate([
  { $group: { _id: "$produto", receita: { $sum: { $multiply: ["$quantidade", "$precoUnitario"] } } } },
  { $sort: { receita: -1 } },
  { $limit: 1 }
]);

// 5.5 Produto mais caro
 db.vendas.aggregate([
  { $sort: { precoUnitario: -1 } },
  { $limit: 1 },
  { $project: { produto: 1, precoUnitario: 1 } }
]);

// 5.6 Faturamento total da empresa
 db.vendas.aggregate([
  { $group: { _id: null, faturamentoTotal: { $sum: { $multiply: ["$quantidade", "$precoUnitario"] } } } }
]);

// 5.7 Vendas realizadas em junho de 2023
 db.vendas.find({ dataVenda: { $gte: "2023-06-01", $lte: "2023-06-30" } }).count();

// 5.8 Vendedor com mais vendas
 db.vendas.aggregate([
  { $group: { _id: "$vendedor", qtdVendas: { $sum: 1 } } },
  { $sort: { qtdVendas: -1 } },
  { $limit: 1 }
]);

// 5.9 Vendedor que gerou mais receita
 db.vendas.aggregate([
  { $group: { _id: "$vendedor", receitaTotal: { $sum: { $multiply: ["$quantidade", "$precoUnitario"] } } } },
  { $sort: { receitaTotal: -1 } },
  { $limit: 1 },
  { $lookup: { from: "funcionarios", localField: "_id", foreignField: "_id", as: "vendedor" } }
]);
```

###  Ajustes / Observações

* Algumas consultas precisaram de adaptação de nomes de campos para evitar retorno vazio.
* Foi necessário validar as datas de vendas para evitar erros de filtragem de junho de 2023.

### Resultados Obtidos

| Nº  | Consulta                        | Resultado                                                                                                                                                                                                |
| --- | ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 5.1 | Produtos vendidos (únicos)      | Capacete do Homem-Formiga, Fake Batarang, Lança-Teias, Laço da Verdade, Nulificador Total, Sabre de Luz (Mace Windu), Sentinelas do Bolivar Trask, Uniforme de Moléculas Instáveis, Uniforme do Superman |
| 5.2 | Produto mais vendido            | Laço da Verdade – 12 unidades                                                                                                                                                                            |
| 5.3 | Produto menos vendido           | Fake Batarang – 2 unidades                                                                                                                                                                               |
| 5.4 | Produto que gerou mais receita  | Sabre de Luz (Mace Windu) – $7.922,32                                                                                                                                                                    |
| 5.5 | Produto mais caro               | Sabre de Luz (Mace Windu) – $990,29/unidade                                                                                                                                                              |
| 5.6 | Faturamento total da empresa    | $27.076,15                                                                                                                                                                                               |
| 5.7 | Vendas realizadas em junho 2023 | 9 vendas                                                                                                                                                                                                 |
| 5.8 | Vendedor com mais vendas        | Jon Deegan – 3 vendas                                                                                                                                                                                    |
| 5.9 | Vendedor que gerou mais receita | Michael Hartstein – $13.256,17                                                                                                                                                                           |

---

##  Conclusões Alcançadas

* Alguns produtos, como **Laço da Verdade** e **Sabre de Luz**, são responsáveis por grande parte da receita, sugerindo foco estratégico.
* Produtos com menor quantidade vendida podem indicar **estoque excessivo ou baixa demanda**, importante para planejamento de compras.
* O faturamento é concentrado em poucos vendedores, permitindo **identificar talentos e alocar incentivos**.
* Monitoramento mensal de vendas, como junho de 2023, ajuda a **identificar sazonalidade e tendências de consumo**.
