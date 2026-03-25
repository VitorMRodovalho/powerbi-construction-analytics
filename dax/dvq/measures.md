| TableName   | Name                             | Expression                                                                                   | DisplayFolder          |   Description |
|:------------|:---------------------------------|:---------------------------------------------------------------------------------------------|:-----------------------|--------------:|
| Medidas     | 001 - SaldoGerencial             | CALCULATE(                                                                                   | Banco Gerencial        |           nan |
|             |                                  |     SUM('Saldos Gerenciais'[Saldo_sdcc]),                                                    |                        |               |
|             |                                  |     LASTNONBLANK(                                                                            |                        |               |
|             |                                  |     VALUES('Saldos Gerenciais'[Data_sdcc]),                                                  |                        |               |
|             |                                  |         DISTINCTCOUNT('Saldos Gerenciais'[Conta_sdcc] ))                                     |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 002 - SaldoGerencialHHistórico   | SUM('Saldos Gerenciais'[Saldo_sdcc])                                                         | Banco Gerencial        |           nan |
| Medidas     | 001 - Unidades Estoque           | IF (                                                                                         | Unidades               |           nan |
|             |                                  |     CALCULATE ( SUM(fEstoque[Estoque]) ) = BLANK (),                                         |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE ( SUM(fEstoque[Estoque]) )                                                     |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 002 - Fora de Venda              | IF (                                                                                         | Unidades               |           nan |
|             |                                  |     CALCULATE ( SUM(fEstoque[Fora_De_Venda]) ) = BLANK (),                                   |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE (SUM(fEstoque[Fora_De_Venda]) )                                                |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 003 - Unidades Quitadas          | IF (                                                                                         | Unidades               |           nan |
|             |                                  |     CALCULATE ( SUM(fEstoque[Quitado]) ) = BLANK (),                                         |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE ( SUM(fEstoque[Quitado]) )                                                     |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 004 - Unidades Ativas            | IF (                                                                                         | Unidades               |           nan |
|             |                                  |     CALCULATE ( SUM(fEstoque[Vendas_Ativas]) ) = BLANK (),                                   |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE ( SUM(fEstoque[Vendas_Ativas]) )                                               |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 005 - Unidades Vendidas          | IF (                                                                                         | Unidades               |           nan |
|             |                                  |     CALCULATE ( SUM(fVendas[Unidades_vendidas]) ) = BLANK (),                                |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE ( SUM(fVendas[Unidades_vendidas]) )                                            |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 001 - Contas a Pagar             | IF(                                                                                          | nan                    |           nan |
|             |                                  |     CALCULATE( SUM('Contas a Pagar'[ValPagar])) = BLANK(),                                   |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE( SUM('Contas a Pagar'[ValPagar]))                                              |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 002 - Contas Recebidas           | IF(                                                                                          | nan                    |           nan |
|             |                                  |     CALCULATE( SUM('Contas Recebidas'[Valor Dep])) = BLANK(),                                |                        |               |
|             |                                  |     0,                                                                                       |                        |               |
|             |                                  |     CALCULATE( SUM('Contas Recebidas'[Valor Dep]))                                           |                        |               |
|             |                                  | )                                                                                            |                        |               |
| Medidas     | 001 - TotalPagamentos            | COUNT('Processos de Pagamento'[TipoProcPag])                                                 | Processos de Pagamento |           nan |
| Medidas     | 002 - Cotacao                    | --VAR A =                                                                                    | Processos de Pagamento |           nan |
|             |                                  | CALCULATE(                                                                                   |                        |               |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 0)                                       |                        |               |
|             |                                  |     )                                                                                        |                        |               |
|             |                                  | --RETURN                                                                                     |                        |               |
|             |                                  | --A/[001 - TotalPagamentos]                                                                  |                        |               |
| Medidas     | 003 - ProcessoPagamento          | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 1)                                       |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 004 - CompraRapida               | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 2)                                       |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 005 - ProcessoVinculado          | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 3)                                       |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 006 - ProcessoTransporte         | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 4)                                       |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 007 - Medicao                    | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 7)                                       |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 008 - AcompEntradaSemEstoque     | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 10)                                      |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 009 - CompPatrimonio             | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 11)                                      |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 010 - AdiamCaixaObra             | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag] = 13)                                      |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | teste                            | VAR A = CALCULATE(                                                                           | nan                    |           nan |
|             |                                  |             COUNT('Processos de Pagamento'[TipoProcPag]),                                    |                        |               |
|             |                                  |             FILTER('Processos de Pagamento',                                                 |                        |               |
|             |                                  |                 SELECTEDVALUE('Processos de Pagamento'[TipoProcPagam])))                     |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | RETURN                                                                                       |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | DIVIDE(A, [001 - TotalPagamentos], 0)                                                        |                        |               |
| Medidas     | Soma Total                       | VAR SomaTotal =                                                                              | nan                    |           nan |
|             |                                  | CALCULATE(                                                                                   |                        |               |
|             |                                  |          [001 - TotalPagamentos],                                                            |                        |               |
|             |                                  |         ALL('Processos de Pagamento'[TipoProcPagam])                                         |                        |               |
|             |                                  |     )                                                                                        |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | RETURN                                                                                       |                        |               |
|             |                                  |     DIVIDE(                                                                                  |                        |               |
|             |                                  |         [001 - TotalPagamentos],                                                             |                        |               |
|             |                                  |         SomaTotal,                                                                           |                        |               |
|             |                                  |         0                                                                                    |                        |               |
|             |                                  |     )                                                                                        |                        |               |
| Medidas     | 011 - Outros                     | CALCULATE(                                                                                   | Processos de Pagamento |           nan |
|             |                                  |     COUNT('Processos de Pagamento'[TipoProcPag]),                                            |                        |               |
|             |                                  |         FILTER('Processos de Pagamento',                                                     |                        |               |
|             |                                  |             'Processos de Pagamento'[TipoProcPag]  IN {2,4,10,11,13,15,17,18,20,21,23,26}    |                        |               |
|             |                                  |     ))                                                                                       |                        |               |
| Medidas     | YTD Mes                          | TOTALMTD( COUNT('Processos de Pagamento'[TipoProcPag]),'dCalendário'[DATA])                  | nan                    |           nan |
| Medidas     | YTD Mes Anterior                 | TOTALMTD( COUNT('Processos de Pagamento'[TipoProcPag]), PREVIOUSMONTH('dCalendário'[DATA]))  | nan                    |           nan |
| Medidas     | VariacaoMesAnterior              | [YTD Mes]-[YTD Mes Anterior]                                                                 | nan                    |           nan |
| Medidas     | VariacaoPercMesAnterior          | 1 - [YTD Mes Anterior]/[YTD Mes]                                                             | nan                    |           nan |
| 0-mEDIDAS   | Processos da Semana              | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |             DATESBETWEEN('dCalendário'[DATA], [Inicio semana], '0-mEDIDAS'[SABADO]))         |                        |               |
| 0-mEDIDAS   | Inicio semana                    | VAR A = TODAY()                                                                              | nan                    |           nan |
|             |                                  |                                                                                              |                        |               |
|             |                                  | VAR B =  WEEKDAY(TODAY())                                                                    |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | VAR C = 0                                                                                    |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | RETURN                                                                                       |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | A - B + 2                                                                                    |                        |               |
| 0-mEDIDAS   | SABADO                           | VAR A = TODAY()                                                                              | nan                    |           nan |
|             |                                  |                                                                                              |                        |               |
|             |                                  | VAR B =  WEEKDAY(TODAY())                                                                    |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | VAR C = 0                                                                                    |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | RETURN                                                                                       |                        |               |
|             |                                  |                                                                                              |                        |               |
|             |                                  | A + 7 - B + 1                                                                                |                        |               |
| 0-mEDIDAS   | Processos Próxima Semana         | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |             DATESBETWEEN('dCalendário'[DATA], [Inicio semana] + 7, '0-mEDIDAS'[SABADO] + 7)) |                        |               |
| 0-mEDIDAS   | Processos em Atraso              | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |             FILTER('dCalendário', 'dCalendário'[DATA] < [Inicio semana]))                    |                        |               |
| 0-mEDIDAS   | Processos da Semana sem Q        | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |             DATESBETWEEN('dCalendário'[DATA], [Inicio semana], '0-mEDIDAS'[SABADO]),         |                        |               |
|             |                                  |             Consulta1[DataConfQdade_Proc] = BLANK())                                         |                        |               |
| 0-mEDIDAS   | Processos da Semana sem D        | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |             DATESBETWEEN('dCalendário'[DATA], [Inicio semana], '0-mEDIDAS'[SABADO]),         |                        |               |
|             |                                  |             Consulta1[DataConfDt_Proc] = BLANK())                                            |                        |               |
| 0-mEDIDAS   | Processos da Semana sem V        | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |             DATESBETWEEN('dCalendário'[DATA], [Inicio semana], '0-mEDIDAS'[SABADO]),         |                        |               |
|             |                                  |             Consulta1[DataConfVl_Proc] = BLANK())                                            |                        |               |
| 0-mEDIDAS   | Processos da Semana - DV sem o Q | CALCULATE(                                                                                   | nan                    |           nan |
|             |                                  |         COUNT(Consulta1[DtVencParc_Proc]),                                                   |                        |               |
|             |                                  |            -- DATESBETWEEN('dCalendário'[DATA], [Inicio semana], '0-mEDIDAS'[SABADO]),       |                        |               |
|             |                                  |             Consulta1[Conf_Proc] = "DVQ")                                                    |                        |               |