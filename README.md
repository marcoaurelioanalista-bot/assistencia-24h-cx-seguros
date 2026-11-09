# 🚑 Gestão de SLAs, NPS e Auditoria de Assistência 24h em Seguros

Análise end-to-end do impacto do tempo de resposta da Assistência 24h (socorro mecânico, reboque e chaveiro) na satisfação do cliente (NPS) e na gestão de prestadores de serviço terceirizados.

---

## 🎯 O Desafio de Negócio

No mercado segurador, o acionamento da Assistência 24h é um dos principais "momentos da verdade" (*moments of truth*) na jornada do cliente. A frustração com atrasos prolongados no atendimento de emergência é a maior causadora de atrito e cancelamentos não planejados.

Este projeto visa responder às seguintes questões estratégicas de operações e CX:
1. **Qual é o limite tolerável de espera?** Identificar o momento exato em que o atraso transforma um cliente em **Detrator**.
2. **Auditoria de Fornecedores:** Mapear quais empresas parceiras (prestadores de guincho/socorro) estão quebrando SLAs contratuais.
3. **Eficiência Regional:** Descobrir gargalos logísticos por região geográfica (Capital, Interior, Litoral).

---

## 🛠️ Arquitetura e Tecnologias Utilizadas

* **Python (Pandas & NumPy):** Criação e modelagem de um dataset sintético de 500 chamados com regras de correlação probabilística entre minutos de atraso e queda no NPS.
* **Power BI & DAX:** Desenho de dashboard executivo com indicadores dinâmicos de NPS, SLA cumprido e auditoria de parceiros.

---

## 📊 Dicionário de Dados

| Campo | Tipo | Descrição |
| :--- | :--- | :--- |
| `ID_Chamado` | Text | Identificador único da solicitação de emergência |
| `Regiao` | Text | Região geográfica do acionamento (Capital, Litoral, etc.) |
| `Servico_Acionado` | Text | Tipo de socorro (Pane Elétrica, Reboque, Chaveiro, etc.) |
| `Prestador` | Text | Empresa parceira responsável pelo atendimento |
| `SLA_Prometido_Min` | Integer | Tempo limite contratual de chegada (em minutos) |
| `Tempo_Real_Min` | Integer | Tempo decorrido até a chegada do prestador |
| `Status_SLA` | Text | Classificação automática: *No Prazo* ou *Atrasado* |
| `Minutos_Atraso` | Integer | Minutos excedentes ao tempo limite contratual |
| `Nota_NPS` | Integer | Pontuação atribuída pelo cliente na pesquisa (1 a 10) |
| `Classificacao_NPS` | Text | Categoria do cliente: *Promotor* (9-10), *Neutro* (7-8) ou *Detrator* (1-6) |

---

## 📈 Principais Fórmulas DAX

### 1. Score de NPS Oficial (-100 a +100)
```dax
NPS Score = 
VAR Qtd_Promotores = CALCULATE(COUNT(df[ID_Chamado]), df[Classificacao_NPS] = "Promotor")
VAR Qtd_Detratores = CALCULATE(COUNT(df[ID_Chamado]), df[Classificacao_NPS] = "Detrator")
VAR Total_Respondentes = COUNT(df[ID_Chamado])
RETURN 
DIVIDE(Qtd_Promotores - Qtd_Detratores, Total_Respondentes, 0) * 100
