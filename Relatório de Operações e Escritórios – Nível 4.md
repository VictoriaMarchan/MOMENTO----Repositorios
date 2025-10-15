# Relatório de Operações e Escritórios – Nível 4

##  Descrição

Este relatório analisa os escritórios da empresa **Momento**, seus custos operacionais e inventário de suprimentos. O objetivo é fornecer informações estratégicas sobre **custos, alocação de suprimentos e eficiência operacional** para a equipe de Operações.

##  Metodologia

Consultas para operações e escritórios utilizam agregações com `$unwind`, `$group` e `$sum` para quantificar o inventário, calcular os custos totais e comparar os escritórios. Este método é eficiente para analisar inventário e despesas logísticas e permite decisões estratégicas relacionadas a estoque, compras e utilização de recursos, além de identificar escritórios mais caros ou aqueles com maior variedade de suprimentos.

---

##  Informações

###  Consultas Utilizadas

```javascript
// 4.1  Liste todos os escritórios e seus respectivos países.
db.escritorios.find({}, { nome: 1, pais: 1, _id: 0 });

// 4.2 Qual é o custo total de suprimentos em cada escritório? Ordene do mais caro ao mais barato.
db.escritorios.aggregate([
  { $unwind: "$suprimentos" },
  { $group: {
      _id: "$nome",
      custoTotal: { $sum: { $multiply: ["$suprimentos.quantidade", "$suprimentos.precoUnitario"] } }
  }},
  { $sort: { custoTotal: -1 } }
]);

// 4.3 Qual escritório possui a maior quantidade de diferentes tipos de suprimentos?
db.escritorios.aggregate([
  { $project: { nome: 1, tiposSuprimentos: { $size: "$suprimentos" } } },
  { $sort: { tiposSuprimentos: -1 } }
]);

// 4.4 Qual é o suprimento mais caro (considerando preço unitário) em toda a empresa?
db.escritorios.aggregate([
  { $unwind: "$suprimentos" },
  { $sort: { "suprimentos.precoUnitario": -1 } },
  { $limit: 1 },
  { $project: { produto: "$suprimentos.produto", precoUnitario: "$suprimentos.precoUnitario" } }
]);

// 4.5 Calcule o valor total do inventário de suprimentos da empresa (quantidade × preço unitário de todos os itens em todos os escritórios).
db.escritorios.aggregate([
  { $unwind: "$suprimentos" },
  { $group: { _id: null, valorTotal: { $sum: { $multiply: ["$suprimentos.quantidade", "$suprimentos.precoUnitario"] } } } }
]);
```

###  Ajustes / Observações

Durante a execução do relatorio, observou-se que algumas quantidades de suprimentos estavam inconsistentes ou faltavam campos de preço unitário em alguns registros. Corrigimos manualmente adicionando valores padrão para não comprometer os cálculos de custo total.

---

### Resultados Obtidos

| Consulta                                     | Resultado                                                                                                             |
| -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| 4.1 Escritórios e países                     | Wayne Offices (USA), Winterfell Offices (IRE), Stark Industries (ENG), Umbrella Corp (EQU)                            |
| 4.2 Custo total de suprimentos               | Wayne Offices: $376.486.500, Umbrella Corp: $189.792,5, Stark Industries: $149.628,75, Winterfell Offices: $149.142,5 |
| 4.3 Maior quantidade de tipos de suprimentos | Wayne Offices – 5 tipos                                                                                               |
| 4.4 Suprimento mais caro                     | Computadores – $5.000/unidade                                                                                         |
| 4.5 Valor total do inventário de suprimentos | $376.975.063,75                                                                                                       |

---

## Conclusões Alcançadas

* Wayne Offices e Stark Industries possuem os **itens mais caros**, impactando significativamente o capital investido em inventário.
* Umbrella Corp possui **quantidade massiva de suprimentos de baixo custo unitário**, gerando um alto valor total.
* A diversificação de suprimentos é concentrada em alguns escritórios, o que sugere **planejamento estratégico de estoque**.
* O custo total elevado de suprimentos evidencia a necessidade de **controle de inventário e otimização de compras**.
* As informações permitem **priorizar redução de custos, redistribuição de itens e planejamento de novos escritórios**.
