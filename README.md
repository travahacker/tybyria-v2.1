> ⚠️ **Repositório arquivado — espelho.** O conteúdo canônico da TybyrIA vive no Hugging Face:
> **[TybyrIA v2.2](https://huggingface.co/Veronyka/tybyria-v2.2)** (modelo canônico e recomendado) ·
> [TybyrIA v2.1](https://huggingface.co/Veronyka/tybyria-v2.1) (legado).
>
> Métricas, limitações e nota metodológica ficam apenas no card do modelo. Este espelho não é atualizado.

---

# 🏳️‍🌈 TybyrIA v2.1 (LEGADO)

> ⚠️ **Este repositório é legado. Use a [TybyrIA v2.2](https://huggingface.co/Veronyka/tybyria-v2.2)**, que em teste held-out atinge **recall 97,3% com precisão 86,3%** (@ threshold 0,40) e corrige os falsos positivos em fala afirmativa da v2.1.

**TybyrIA v2.1** é um modelo de detecção de discurso de ódio contra pessoas LGBTQIA+ em português brasileiro, publicado em 28/10/2025 pela [Código Não Binário](https://www.codigonaobinario.org) e hoje substituído pela v2.2.

- **Pesos e model card**: https://huggingface.co/Veronyka/tybyria-v2.1
- **Modelo atual (canônico)**: https://huggingface.co/Veronyka/tybyria-v2.2
- **Dataset**: https://huggingface.co/datasets/Veronyka/base-dados-odio-lgbtqia (acesso gated)

## 🔍 Nota metodológica (19/07/2026)

README revisado em 19/07/2026: as métricas abaixo são de **teste held-out** (190 exemplos nunca usados no treino). Valores divulgados anteriormente ("98,44% de recall") vinham de avaliação que incluía dados de treino e não devem ser usados como referência.

**Métricas da v2.1** (held-out, 71 casos de ódio):

| Threshold | Recall | Precisão | Acurácia | F1 |
|---|---|---|---|---|
| 0,30 (o publicado) | 91,5% | 53,7% | 67,4% | 67,7% |
| 0,45 | 80,3% | 78,1% | 84,2% | 79,2% |

No threshold 0,30 o modelo marca como ódio 10/12 frases de elogio/afirmação LGBTQIA+ do conjunto de sondagem (corrigido na v2.2 para 1/12). Toda saída exige revisão humana; não use para ação automática.

## 🎯 Uso recomendado

Não recomendamos a v2.1 para novos usos — prefira a [v2.2](https://huggingface.co/Veronyka/tybyria-v2.2). Se você já usa a v2.1: trate toda classificação como candidata a revisão humana (quase metade dos alertas é falso positivo) e tenha atenção especial a conteúdo afirmativo sobre pessoas LGBTQIA+, onde o modelo mais erra.

## 🔬 Metodologia

- **Base**: `FpOliveira/tupi-bert-base-portuguese-cased` (Tupi-BERT-Base), fine-tuned para detecção de ódio anti-LGBTQIA+
- **Dataset**: 1.891 comentários do Instagram anotados manualmente (37,2% ódio); split 80/10/10
- **Loss**: Focal Loss (α=0.75, γ=2.0) | **Épocas**: 4 | **LR**: 1e-5 | **Batch**: 16 (efetivo)
- **Hardware**: Apple M2 (MPS), ~32 minutos de treino

## 💻 Como usar

```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification
import torch

model_name = "Veronyka/tybyria-v2.1"  # legado — prefira Veronyka/tybyria-v2.2
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForSequenceClassification.from_pretrained(model_name)

text = "Seu texto aqui"
inputs = tokenizer(text, return_tensors="pt", truncation=True, max_length=256)
with torch.no_grad():
    probs = torch.softmax(model(**inputs).logits, dim=-1)
hate_prob = probs[0][1].item()
print(f"Probabilidade de ódio: {hate_prob:.2%} — revisão humana obrigatória")
```

## ⚠️ Limitações

1. Falsos positivos altos (38,9% dos alertas na avaliação original; precisão held-out 53,7% @0,30) — revisão humana obrigatória.
2. Marca fala afirmativa LGBTQIA+ como ódio (10/12 frases de sondagem @0,30) — corrigido na v2.2.
3. Contexto limitado a 256 tokens; dificuldade com ironia/sarcasmo.
4. Português brasileiro; treinado em Instagram; focado em LGBTQIA+fobia (não cobre bem outros tipos de ódio).

## 📄 Licença

Pesos e artefatos do modelo: **MIT**. Dataset de treino: **CC-BY-NC-SA-4.0** (ver card do dataset).

## 📚 Citação

```bibtex
@software{tybyria_v21,
  title={TybyrIA v2.1: Sistema de Detecção de Discurso de Ódio LGBTQIA+ (legado)},
  author={{Código Não Binário}},
  year={2025},
  url={https://huggingface.co/Veronyka/tybyria-v2.1},
  note={Métricas held-out (corrigidas em 19/07/2026): recall 91,5\%, precisão 53,7\% @ threshold 0,30.
        Substituído por TybyrIA v2.2.}
}
```

---

**🏳️‍🌈 Desenvolvido com orgulho para a comunidade LGBTQIA+ brasileira** — [Código Não Binário](https://www.codigonaobinario.org)

**Versão**: 2.1.0 (legado) · **Publicado**: 28/10/2025 · **README corrigido**: 19/07/2026
