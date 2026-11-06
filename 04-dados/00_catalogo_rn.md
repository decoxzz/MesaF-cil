# Catálogo de Regras de Negócio — MesaFácil

**Projeto:** MesaFácil — Pedidos de Restaurante  
**Grupo:** 03  
**Integrante responsável por esta contribuição:** André Gonzato Paz  
**Disciplina:** Requisitos, Modelagem e Análise de Dados  
**Versão:** 1.0  
**Data:** 06/11/2026

## Objetivo

Este documento formaliza as regras de negócio identificadas no cenário do projeto MesaFácil e indica como cada regra deve ser considerada na modelagem dos dados. As regras foram extraídas do cenário definido no edital do projeto.

## RN-01 — Cálculo da conta da mesa

**Regra:** O valor da conta de uma mesa corresponde à soma dos preços dos itens pedidos, multiplicados pelas respectivas quantidades.

**Representação na modelagem:**
- **Atributo derivado:** `valor_total_conta`, associado conceitualmente à conta da mesa. Seu valor é calculado a partir dos itens incluídos nos pedidos vinculados à mesa.
- **Relacionamentos envolvidos:** Mesa possui pedidos; Pedido contém itens do cardápio por meio da entidade associativa `ItemPedido`.
- **Cálculo:** para cada item pedido, multiplicar o preço registrado para aquele item pela quantidade e somar os resultados.

**Observação:** por ser um valor calculável a partir dos itens dos pedidos, `valor_total_conta` deve ser identificado como atributo derivado no modelo conceitual. A equipe deve decidir no modelo lógico se ele será calculado em consulta ou armazenado por necessidade técnica, mantendo a regra de cálculo como referência.

## RN-02 — Mesa ocupada para receber pedidos

**Regra:** Uma mesa somente pode receber novos pedidos enquanto estiver com status **ocupada**.

**Representação na modelagem:**
- **Atributo:** `status` na entidade `Mesa`, com valores que representem, no mínimo, mesa livre e mesa ocupada, conforme o cenário.
- **Restrição de integridade/regra de negócio:** a inclusão de um novo `Pedido` para uma `Mesa` deve ser permitida somente quando `Mesa.status = 'ocupada'`.
- **Relacionamento envolvido:** cada pedido deve estar associado à mesa à qual pertence.

**Observação:** essa condição não é expressa apenas pela cardinalidade do relacionamento. É uma restrição condicional de negócio que precisa ser documentada e considerada nas validações do sistema.

## RN-03 — Preservação do preço no momento do pedido

**Regra:** O preço cobrado por um item deve ser o preço do cardápio válido no momento em que o pedido é realizado.

**Representação na modelagem:**
- **Atributo:** `preco_unitario` na entidade associativa `ItemPedido`.
- **Relacionamento envolvido:** `Pedido` e `ItemCardapio` possuem relacionamento N:N, resolvido por `ItemPedido`.
- **Restrição de integridade/regra de negócio:** ao registrar um item no pedido, o sistema deve copiar para `ItemPedido.preco_unitario` o preço vigente em `ItemCardapio` naquele momento. Alterações posteriores no preço do cardápio não devem modificar o preço já registrado no pedido.

**Observação:** o atributo `preco_unitario` em `ItemPedido` preserva o histórico do valor cobrado. O preço atual do cardápio continua pertencendo à entidade `ItemCardapio`; os dois valores têm finalidades diferentes.

## Resumo do mapeamento

| Regra | Elemento principal | Como é aplicada |
|---|---|---|
| RN-01 | Atributo derivado `valor_total_conta` e relacionamentos entre Mesa, Pedido e ItemPedido | Soma de `preco_unitario × quantidade` dos itens pedidos para a mesa |
| RN-02 | Atributo `Mesa.status` e restrição de integridade | Só permite incluir pedido quando a mesa estiver ocupada |
| RN-03 | Atributo `ItemPedido.preco_unitario` e restrição de integridade | Registra o preço vigente no momento da inclusão do item no pedido |

## Pontos para validação da equipe

1. Confirmar os nomes finais das entidades e atributos no modelo conceitual e no modelo lógico.
2. Manter `ItemPedido` como entidade associativa para resolver o relacionamento N:N entre `Pedido` e `ItemCardapio`.
3. Identificar `valor_total_conta` como atributo derivado no modelo conceitual.
4. Documentar a validação de status da mesa e a preservação do preço histórico nas regras de integridade.

---

**Responsável pela elaboração deste catálogo:** André Gonzato Paz  
**Nota:** este catálogo formaliza as três regras explícitas do cenário do edital. A equipe deve manter a nomenclatura consistente com a versão final dos modelos.
