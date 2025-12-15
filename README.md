---
license: cc-by-nc-sa-4.0
language:
- pt
tags:
- hate-speech-detection
- lgbtqia+
- portuguese
- bert
- text-classification
- tupi-bert
- ódio
- hate
- queer
- lgbt
- trans
- lgbtphobia
- homophobia
- queerpgobia
- lgbtfobia
- homofobia
datasets:
- Veronyka/base-dados-odio-lgbtqia
metrics:
- accuracy
- precision
- recall
- f1
pipeline_tag: text-classification
base_model:
- FpOliveira/tupi-bert-base-portuguese-cased
pinned: true
---

# 🏳️‍🌈 TybyrIA v2.1

**TybyrIA v2.1** é um modelo de inteligência artificial especializado em detecção de discurso de ódio contra pessoas LGBTQIA+ em português brasileiro, com **98.44% de recall** para minimizar casos não detectados.

> **Nota**: Este modelo faz parte do ecossistema **Radar Social LGBTQIA+**, uma aplicação que utiliza TybyrIA para análise de conteúdo em redes sociais.

## 📊 Performance

| Métrica | Valor | Interpretação |
|---------|-------|---------------|
| **Recall** | **98.44%** ✅ | Detecta 692 de 703 casos de ódio |
| **Accuracy** | 76.15% | Acurácia geral no conjunto de teste |
| **Precision** | 61.13% | 6 em 10 alertas são ódio real |
| **F1-Score** | 75.42% | Equilíbrio entre precisão e recall |
| **Threshold** | 0.30 | Otimizado para máximo recall |

### Interpretação Prática

- ✅ **Detecta 98 de cada 100 casos de ódio**
- ⚠️ **11 casos de ódio podem passar** (de 703 totais)
- 📊 **440 falsos positivos** (~23% dos alertas requerem revisão)

## 🎯 Uso Recomendado

### Níveis de Confiança

- 🔴 **ALTA** (prob >= 50%): Ação automática recomendada (Precision ~84%)
- 🟡 **MÉDIA** (prob 30-50%): Revisão humana recomendada
- 🟢 **BAIXA** (prob < 30%): Conteúdo OK

### Para Moderação de Conteúdo

Este modelo é otimizado para **minimizar falsos negativos** (ódio não detectado), priorizando recall sobre precisão. Recomendamos:

1. **Casos >= 50%**: Bloquear/remover automaticamente
2. **Casos 30-50%**: Enviar para fila de revisão humana
3. **Casos < 30%**: Deixar passar

## 🔬 Metodologia

### Modelo: TybyrIA v2.1

- **TybyrIA v2.1**: Fine-tuned especializado em hate speech LGBTQIA+
- **Base**: `FpOliveira/tupi-bert-base-portuguese-cased` (Tupi-BERT-Base)
- **Tipo**: BERT pré-treinado em português, fine-tuned para detecção de ódio LGBTQIA+

### Treinamento

- **Dataset**: 1.891 comentários do Instagram
- **Anotação**: Manual pela [Código Não Binário](https://www.codigonaobinario.org)
- **Divisão**: 80% treino, 10% validação, 10% teste
- **Épocas**: 4
- **Loss**: Focal Loss (α=0.75, γ=2.0) para lidar com desbalanceamento
- **Learning Rate**: 1e-5
- **Batch Size**: 16 (efetivo)
- **Hardware**: Apple M2 (MPS) - 32 minutos de treino

### Otimização de Threshold

- Testados thresholds de 0.15 a 0.60
- Threshold 0.30 maximiza recall mantendo precisão aceitável
- Validado em conjunto separado via curva Precision-Recall

## 💻 Como Usar

### Instalação

```bash
pip install transformers torch
```

### Uso Básico

```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification
import torch

# Carregar modelo e tokenizer TybyrIA v2.1
model_name = "Veronyka/tybyria-v2.1"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForSequenceClassification.from_pretrained(model_name)

# Texto para análise
text = "Seu texto aqui"

# Tokenizar e fazer predição
inputs = tokenizer(text, return_tensors="pt", truncation=True, max_length=256)

with torch.no_grad():
    outputs = model(**inputs)
    probs = torch.softmax(outputs.logits, dim=-1)
    hate_prob = probs[0][1].item()

# Aplicar threshold otimizado
THRESHOLD = 0.30
is_hate = hate_prob >= THRESHOLD

# Interpretar resultado
if hate_prob >= 0.50:
    confidence = "🔴 ALTA"
    action = "Ação automática recomendada"
elif hate_prob >= 0.30:
    confidence = "🟡 MÉDIA"
    action = "Revisão humana recomendada"
else:
    confidence = "🟢 BAIXA"
    action = "Conteúdo OK"

print(f"Probabilidade de ódio: {hate_prob:.2%}")
print(f"Confiança: {confidence}")
print(f"Ação: {action}")
```

### Pipeline Simplificado

```python
from transformers import pipeline

classifier = pipeline(
    "text-classification",
    model="Veronyka/tybyria-v2.1",
    tokenizer="Veronyka/tybyria-v2.1"
)

result = classifier("Seu texto aqui")
print(result)
```

## 📊 Dataset

O modelo foi treinado com dados da [Base de Dados de Ódio LGBTQIA+](https://huggingface.co/datasets/Veronyka/base-dados-odio-lgbtqia):

- **Total**: 1.891 comentários
- **Fonte**: Instagram (Podcast Entre Amigues)
- **Anotação**: Manual com 33 categorias de análise
- **Distribuição**: 37.2% ódio, 62.8% não-ódio
- **Licença**: CC-BY-NC-SA-4.0

### Tipos de Ódio Detectados

- Transfobia
- Homofobia
- Lesbofobia
- Bifobia
- Intersexofobia
- LGBTfobia geral
- Assédio e insultos
- Ameaças e incitação à violência
- Patologização e pseudociência
- Desumanização e animalização
- Misgendering e deadnaming

## 🔍 Comparação V2.0 → V2.1

| Métrica | V2.0 | V2.1 | Melhoria |
|---------|------|------|----------|
| **Recall** | 5% ❌ | **98.44%** ✅ | **+93.44pp** 🚀 |
| **Ódio detectado** | 36/703 | 692/703 | **+656 casos** |
| **Ódio que escapa** | 667 | **11** | **-656 casos** ✅ |
| **Accuracy** | 64% | 76.15% | +12.15pp |
| **F1-Score** | - | 75.42% | - |

## ⚠️ Limitações

1. **Falsos Positivos**: ~23% dos alertas podem não ser ódio real
   - **Solução**: Revisão humana para casos com prob. 0.30-0.50

2. **Contexto Limitado**: Máximo de 256 tokens
   - Textos muito longos são truncados

3. **Ironia/Sarcasmo**: Dificuldade em detectar
   - Exemplo: "😂😂😂" pode ser ambíguo

4. **Idioma**: Português brasileiro apenas
   - Performance pode cair em PT-PT ou outros dialetos

5. **Domínio**: Treinado em Instagram
   - Performance pode variar em outras plataformas

6. **Tipos de Ódio**: Focado em LGBTQIA+fobia
   - Não detecta bem racismo, xenofobia, etc.

## 🚀 Spaces e Demos

- **Interface V2.1**: [radar-social-lgbtqia-v2.1](https://huggingface.co/spaces/Veronyka/radar-social-lgbtqia-v2.1) (utiliza TybyrIA v2.1)

## 📚 Citação

Se você usar este modelo em pesquisa ou produção, por favor cite:

```bibtex
@software{tybyria_v21,
  title={TybyrIA v2.1: Sistema de Detecção de Discurso de Ódio LGBTQIA+},
  author={Código Não Binário - https://codigonaobinario.org},
  year={2025},
  url={https://huggingface.co/Veronyka/tybyria-v2.1},
  note={TybyrIA v2.1 (base: Tupi-BERT-Base + Fine-tuning Focal Loss), 
        Dataset: Base de Dados de Ódio LGBTQIA+ (Podcast Entre Amigues - https://linktr.ee/entre_amigues),
        Threshold: 0.30 (optimized for 98.44\% recall)}
}
```

## 📄 Licença

**CC-BY-NC-SA-4.0** (Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International)

- ✅ **Uso acadêmico**: Livre para pesquisa e educação
- ✅ **Atribuição**: Creditar Código Não Binário
- ✅ **Compartilhamento igual**: Derivações sob mesma licença
- ⚠️ **Não comercial**: Contato necessário para uso comercial

O dataset e o modelo compartilham a mesma licença CC-BY-NC-SA-4.0 para garantir consistência e proteção dos dados da comunidade LGBTQIA+.

## 🤝 Contribuições

Este modelo foi desenvolvido pela [Código Não Binário](https://codigonaobinario.org) como parte do projeto Radar Social LGBTQIA+, uma iniciativa para combater o discurso de ódio contra pessoas LGBTQIA+ na internet brasileira.

### Agradecimentos

- **FpOliveira**: Pelo modelo Tupi-BERT base (`FpOliveira/tupi-bert-base-portuguese-cased`)
- **Hugging Face**: Pela infraestrutura
- **Comunidade LGBTQIA+**: Pela resiliência e resistência

---

**🏳️‍🌈 Desenvolvido com orgulho para a comunidade LGBTQIA+ brasileira**

**Versão**: 2.1.0  
**Data**: 28/10/2025  
**Contato**: Via Hugging Face ou em codigonaobinario.org