# 📦 Calculadora de LEF (Lote Econômico de Fabricação) — do problema à decisão em 5 números

Mini-app em **Python** (Colab-ready) que calcula o **LEF** para itens fabricados com **reposição progressiva (P > D)** e exibe somente os **5 indicadores essenciais** para tomada de decisão:

1) **LEF (unid./lote)**  
2) **Nº de lotes por período**  
3) **Custo de setup (período)**  
4) **Custo de manutenção (período)**  
5) **Custo total (período)**

> Resultados no padrão BR: milhar **`.`** e decimal **`,`**. Interface com formulário (ipywidgets) no Colab e **fallback em modo texto** no terminal.

---

## ✨ Recursos
- 🎯 **Foco em 5 indicadores** (sem ruído)  
- 🇧🇷 **Formatação BR** (ex.: `248.423` / `R$ 13.042,24`)  
- 🧰 **Colab-ready**: instala/ativa `ipywidgets` automaticamente  
- 🖥️ **Modo texto (CLI)** se você rodar localmente sem notebook  
- 🧪 **Exemplo clássico** para validar rapidamente
- 🧱 **Código didático** com `dataclasses` para entradas/saídas

---

## 🧠 Conceitos
Este projeto implementa o **LEF** para cenários em que **P > D** (a produção excede a demanda). Ao produzir, forma-se estoque; entre lotes, ele é consumido. O **LEF** equilibra **custo de setup (Cp)** e **custo de manutenção** (*t × Cunit × estoque médio*), **minimizando** o **custo total por período**.

### Entradas (formulário)
- **D**: demanda por período (unid/tempo). Ex.: `3.600.000` unid/ano  
- **P**: produção por período (unid/tempo). **Deve ser > D**. Ex.: `7.200.000` unid/ano  
- **t**: taxa de manutenção do estoque no período (fração). Ex.: `0,30` (= 30% a.a.)  
- **Cunit**: custo unitário (R$/unid). Ex.: `0,35` → R$ 0,35/unid  
- **Cp**: custo de setup por lote (R$). Ex.: `450` → R$ 450 por troca

> **Checklist**: D e P na **mesma base de tempo**; **P > D**; `t` em fração (0,30 = 30%); `Cunit` e `Cp` em R$.

### Saídas (o que significa cada uma)
- **LEF (unid./lote)**: tamanho ótimo do lote para minimizar o custo total.  
- **Nº de lotes por período**: frequência de produção no período.  
- **Custo de setup (período)**: custo total com trocas de lote.  
- **Custo de manutenção (período)**: custo de manter o estoque médio que se forma ao produzir.  
- **Custo total (período)**: soma de setup + manutenção (**mínimo** no LEF).

---

## 🧮 Fórmulas implementadas
Com `f = (1 − D/P)`:

- **LEF** = `sqrt( (2 × Cp × D) / ( t × Cunit × f ) )`  
- **Estoque médio (Em)** = `0,5 × LEF × f`  
- **Nº de lotes (N)** = `D / LEF`  
- **Custo de manutenção** = `t × Cunit × Em`  
- **Custo de setup** = `Cp × (D / LEF)`  
- **Custo total** = `Custo de manutenção + Custo de setup`

> Hipóteses: demanda e produção constantes; **P > D**; `t` e `Cp` constantes; sem rupturas/ressuprimentos urgentes; sem descontos por volume.

---

## 🚀 Como usar

### Opção A — Google Colab (recomendada)
1. Abra um notebook novo no **Google Colab**.  
2. **Cole o bloco único do projeto** em **uma célula**.  
3. Execute a célula. Se o Colab instalar `ipywidgets` na primeira vez, **execute novamente**.  
4. Preencha **D, P, t, Cunit, Cp** e clique em **“Calcular LEF + Tabela”**.

### Opção B — Local (modo texto/CLI)
1. Tenha **Python 3.10+** instalado.  
2. Instale dependências mínimas:
   ```bash
   pip install pandas
   ```
3. Salve o código como `lef_app.py` e rode:
   ```bash
   python lef_app.py
   ```
4. Siga as instruções no terminal (o app entra em **fallback_cli**).

---

## 🧩 Exemplo rápido (validação)
Entradas (base anual): `D=3.600.000`, `P=7.200.000`, `t=0,30`, `Cunit=0,35`, `Cp=450`

**Saída esperada (aprox.):**

| Indicador | Valor |
|---|---|
| **LEF (unid./lote)** | **248.423** |
| **Nº de lotes por período** | **14,49** |
| **Custo de setup (período)** | **R$ 6.521,12** |
| **Custo de manutenção (período)** | **R$ 6.521,12** |
| **Custo total (período)** | **R$ 13.042,24** |

**Interpretação:** produza **lotes de ~248 mil peças**; serão **~14,5 lotes** no ano; o custo total mínimo é **~R$ 13 mil/ano** (setup ≈ manutenção no ótimo).

---

## 🧱 Estrutura do código (principais funções)
- `EntradasLEF` / `SaidasLEF` *(dataclasses)* — tipagem clara das entradas/saídas.  
- `calcular_lef(e)` — valida e aplica as fórmulas do LEF.  
- `gerar_tabela(s)` — constrói a **tabela** dos 5 indicadores com formatação BR.  
- `ui()` — formulário com `ipywidgets` (Colab).  
- `fallback_cli()` — modo texto (terminal).  
- `exemplo()` — caso clássico para validação.

---

## 🧰 Bibliotecas utilizadas
- **math** — operações matemáticas (`sqrt`) para o LEF.  
- **pandas** — construção/exibição da **tabela** (DataFrame) e fácil exportação futura.  
- **dataclasses** — modelos de dados para entradas/saídas, deixando o código legível e seguro.  
- **ipywidgets** *(no Colab)* — interface com campos e botão.  
- **IPython.display** *(no Colab)* — exibição amigável de HTML e tabela.

---

## ❗️Erros comuns (e como resolver)
- **“P deve ser MAIOR que D”** → Ajuste P ou D; o modelo exige **P > D**.  
- **Números com vírgula** → No terminal, o código já converte `,` para `.`. No formulário, use **ponto** (ex.: `0.30`).  
- **Botão não reage no Colab** → Após instalar `ipywidgets` na 1ª execução, **rode a célula novamente**.  
- **Unidades** → Garanta **D e P** na **mesma base de tempo** (dia/mês/ano).

---

## 🗺️ Próximos passos
- Exportar resultados para Excel: `df.to_excel("resultado_lef.xlsx", index=False)`  
- Sensibilidade: varrer **t**, **Cp**, **P** e comparar o **CT**.  
- Versão multi-SKU (pipeline por produto) e restrições de capacidade/turnos.

---

## 📄 Licença
Distribuído sob a **MIT License**. Use, adapte e compartilhe livremente, mantendo os créditos.

---

## 🙌 Créditos
Projeto educacional para **PCP e Custos**. Baseado em práticas clássicas de **LEF/EOQ** e implementado em **Python** com foco em clareza e tomada de decisão.
