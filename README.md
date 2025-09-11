# 🏆 Computer Vision Competition - Document Classification

## 📋 Project Overview

이 프로젝트는 **문서 분류 경진대회**를 위한 **완전 자동화된 머신러닝 파이프라인**입니다.

RTX 4090부터 GTX 1660까지 다양한 GPU 환경을 자동으로 감지하고 최적화하여,

원클릭으로 전체 파이프라인(학습 → 검증 → 추론 → 제출파일 생성)을 실행할 수 있는 시스템입니다.


### 🎯 프로젝트 특징
- 🔧 **자동화**: GPU 환경 감지 → 최적 설정 → 자동 실행
- 🤝 **환경 호환**: 다양한 GPU 환경 통합 지원 (RTX 4090 ↔ GTX 1660)
- ⚡ **고성능**: ConvNeXt Base 384 (F1: 0.969+) + 단일 폴드 최적화
- 📊 **모니터링**: WandB 통합 실시간 추적 및 자동 시각화
- 🔄 **재현성**: 완전한 실험 추적 및 재현 가능한 결과
- 🎨 **시각화**: 학습/추론/최적화 과정 자동 차트 생성 및 저장
- 🚀 **최신 기법**: 단일 폴드 하이퍼파라미터 최적화 (2초/trial)

### 🏆 최고 성능 기록 (2025-09-10 업데이트)
- **🥇 최고 F1 Score**: **0.98362** (ConvNeXt Base 384, epoch 150, 23분) 🎆
- **🥈 2위**: 0.97918 (ConvNeXt Base 384, epoch 300) 
- **🥉 3위**: 0.96909 (ConvNeXt Base 384, epoch 100)
- **최적화 속도**: 매 trial 2초 완료 (데이터셋 캐싱 적용)
- **경진대회 전략**: 단일 폴드 + 하이퍼파라미터 최적화 (K-fold 대비 6배 빠름)

---

## 🛠️ Quick Start

### 📦 Installation & Setup

1. **Repository Clone**
```bash
git clone <repository-url>
cd computer-vision-competition-1SEN
```

2. **Python Environment (pyenv 권장)**
```bash
pyenv install 3.11.9
pyenv virtualenv 3.11.9 cv_py3_11_9
pyenv activate cv_py3_11_9
pip install -r requirements.txt
```

3. **Data Preparation**
```bash
# 데이터 디렉토리 구조 확인
data/raw/
├── train/          # 학습 이미지
├── test/           # 테스트 이미지  
├── train.csv       # 학습 라벨
└── sample_submission.csv  # 제출 샘플
```

### 🚀 최신 단일 폴드 최적화 (권장 - 매우 빠름!)

**2025년 9월 10일 신규 추가된 고속 최적화 방법**

```bash
# 🏆 경진대회 우승 전략: 단일 폴드 + 하이퍼파라미터 최적화
python src/training/train_main.py \
    --config configs/train_highperf.yaml \
    --mode full-pipeline \
    --use-calibration \
    --optimize \
    --optuna-config configs/optuna_single_fold_config.yaml \
    --auto-continue
```

**특징:**
- ⚡ **초고속**: 매 trial 2초 완료 (데이터셋 캐싱)
- 🎯 **실제 성능**: F1 0.947-0.969 달성 가능
- 🔧 **자동화**: 최적화 완료 후 자동으로 전체 학습 진행
- 💾 **효율성**: K-fold 대비 6배 빠른 실행 시간

### ⚡ 기본 파이프라인

#### 1. 기본 학습 (빠른 프로토타이핑)
```bash
# 기본 설정으로 빠른 학습
python src/training/train_main.py --config configs/train.yaml --mode basic
```

#### 2. 고성능 학습 (최고 성능 추구)
```bash
# 고성능 설정으로 정밀 학습  
python src/training/train_main.py \
    --config configs/train_highperf.yaml \
    --mode full-pipeline \
    --use-calibration
```

#### 3. 하이퍼파라미터 최적화 (기존 K-fold 방식)
```bash
# 전통적인 K-fold 최적화 (느림)
python src/training/train_main.py \
    --config configs/train_highperf.yaml \
    --optimize \
    --n-trials 20 \
    --auto-continue
```

### 🔮 추론 실행

```bash
# 기본 추론
python src/training/train_main.py \
    --config configs/infer_highperf.yaml \
    --mode infer

# 고성능 TTA 추론 (Essential TTA)
python src/training/train_main.py \
    --config configs/infer_highperf.yaml \
    --mode infer \
    --tta essential

# 최고 성능 TTA 추론 (Comprehensive TTA)  
python src/training/train_main.py \
    --config configs/infer_highperf.yaml \
    --mode infer \
    --tta comprehensive
```

---

## 🏗️ 프로젝트 아키텍처

### 📂 디렉토리 구조
```
computer-vision-competition-1SEN/
├── configs/                                     # 설정 파일 모음
│   ├── train.yaml                               # 기본 학습 설정
│   ├── train_highperf.yaml                      # 고성능 학습 설정 
│   ├── infer_highperf.yaml                      # 고성능 추론 설정
│   ├── optuna_single_fold_config.yaml           # 단일 폴드 최적화 설정 (신규)
│   └── 20250910/                                # 백업 설정 파일들
│       ├── train_optimized_*_0908.yaml          # F1 0.969 달성 설정
│       └── ...
│
├── src/                                         # 핵심 소스 코드
│   ├── training/
│   │   ├── train_main.py                        # 통합 실행 인터페이스
│   │   ├── train_highperf.py                    # 고성능 학습 로직
│   │   └── train.py                             # 기본 학습 로직
│   ├── optimization/
│   │   ├── optuna_tuner.py                      # 하이퍼파라미터 최적화 (캐싱 적용)
│   │   ├── test_single_fold_quick.py            # 단일 폴드 테스트
│   │   └── test_optuna_single_fold.py           # Optuna 최적화 테스트
│   ├── models/
│   │   └── build.py                             # 모델 아키텍처 빌더
│   ├── data/
│   │   ├── dataset.py                           # 데이터셋 및 로더
│   │   └── transforms.py                        # 데이터 증강
│   └── inference/
│       └── infer_highperf.py                    # 고성능 추론 로직
│
├── experiments/                                 # 실험 결과 저장
│   ├── train/20250910/20250910_0908_*/          # F1 0.969 실험 결과
│   ├── optimization/                            # 최적화 결과
│   └── infer/                                   # 추론 결과
│
├── logs/                                        # 로그 파일들
│   └── 20250910/train/                          # 학습 로그 (성능 기록 포함)
│
├── submissions/                                 # 제출 파일들
├── docs/                                        # 상세 문서들
│   ├── 시스템/기본_vs_고성능_파이프라인_비교분석.md   # 파이프라인 비교 분석
│   ├── 파이프라인/학습_파이프라인_가이드.md          # 학습 가이드
│   ├── 파이프라인/추론_파이프라인_가이드.md          # 추론 가이드
│   └── 파이프라인/전체_파이프라인_가이드.md          # 전체 가이드
└── README.md                                    # 프로젝트 소개
```

### 🔧 핵심 기술 스택

| 구분 | 기본 파이프라인 | 고성능 파이프라인 |
|-----|--------------|------------------|
| **모델** | EfficientNet B3 | ConvNeXt Base 384 ⭐ |
| **교차검증** | 5-Fold CV | 단일 폴드 (80:20) + 앙상블 |
| **데이터 증강** | 기본 증강 | Hard Augmentation + Mixup |
| **최적화** | 기본 Optuna | 캐싱된 단일 폴드 최적화 🚀 |
| **예상 성능** | F1 ~0.97 | F1 0.983+ ⭐ |
| **실행 시간** | 1-2시간 | 40분 (최적화 포함) |

---

## 📊 성능 및 벤치마크

### 🏆 최고 성능 기록 (2025-09-10)

#### ConvNeXt Base 384 - F1: 0.98362 ⭐
```yaml
실행 시간: 2025-09-10 12:13~12:28 (약 20분)
설정 파일: configs/20250910/train_optimized_*_1213.yaml
성능 진행: Epoch 1: 0.056 → Epoch 10: 0.901 → Final: 0.983
핵심 기법: 고급 증강 + Mixup + EMA + Temperature Scaling
```

#### 단일 폴드 최적화 성능 (신규)
```yaml
매 Trial 실행 시간: 2초
20 Trials 총 시간: 2분  
최고 달성 F1: 0.9478 (Trial 6)
최적 하이퍼파라미터:
  - lr: 6.99e-05
  - batch_size: 16
  - weight_decay: 0.059
  - dropout: 0.267
```

### ⚡ 실행 모드별 성능 비교

| 실행 명령어 | 시간 | 예상 F1 | GPU 메모리 | 추천 상황 |
|------------|------|---------|------------|-----------|
| `--mode basic` | 30분 | 0.920-0.930 | 8GB | 빠른 프로토타입 |
| `--mode highperf` | 2시간 | 0.950-0.965 | 16GB | 최종 제출용 |
| **단일 폴드 최적화** | **40분** | **0.947-0.969** | **12GB** | **⚡ 경진대회용** |

---

## 🚀 고급 사용법

### 1. 실험 추적 및 재현

```bash
# 특정 실험 재현
python src/training/train_main.py \
    --config configs/20250910/train_optimized_20250910_0908.yaml \
    --mode full-pipeline

# WandB 로깅 포함
python src/training/train_main.py \
    --config configs/train_highperf.yaml \
    --mode full-pipeline \
    --wandb-project document-classification-highperf
```

### 2. 커스텀 최적화

```bash  
# 커스텀 Trial 수 및 타임아웃
python src/training/train_main.py \
    --config configs/train_highperf.yaml \
    --optimize \
    --optuna-config configs/optuna_single_fold_config.yaml \
    --n-trials 50 \
    --timeout 7200
```

### 3. 앙상블 추론

```bash
# 여러 모델 앙상블 추론
python src/training/train_main.py \
    --config configs/infer_highperf.yaml \
    --mode infer \
    --ensemble-models experiments/train/20250910/*/ckpt/best_*.pth
```

---

## 📚 상세 문서

### 📖 파이프라인 가이드
- [🎓 학습 파이프라인 가이드](docs/파이프라인/학습_파이프라인_가이드.md) - 학습 과정 상세 설명
- [🔮 추론 파이프라인 가이드](docs/파이프라인/추론_파이프라인_가이드.md) - 추론 및 TTA 설명  
- [🌟 전체 파이프라인 가이드](docs/파이프라인/전체_파이프라인_가이드.md) - 전체 워크플로우

### 🔧 기술 문서
- [⚙️ 기본 vs 고성능 파이프라인 비교](docs/시스템/기본_vs_고성능_파이프라인_비교분석.md) - 파이프라인 상세 비교
- [🔧 모델 설정 가이드](docs/모델/모델_설정_가이드.md) - 모델 구성 및 설정
- [⚡ GPU 최적화 가이드](docs/최적화/GPU_최적화_가이드.md) - GPU 환경 최적화

### 📊 분석 문서  
- [🏆 ConvNeXt 최고성능 분석](docs/학습결과/ConvNeXt_최고성능_학습결과_분석_20250910.md) - F1 0.969 달성 분석
- [📈 경진대회 최적 학습전략](docs/대회전략분석/경진대회_최적학습전략_비교분석_20250910.md) - 전략 비교

---

## 🛠️ 트러블슈팅

### 일반적인 문제들

#### 1. GPU 메모리 부족
```bash  
# 배치 크기 자동 조정
python src/utils/auto_batch_size.py --config configs/train_highperf.yaml

# 수동 배치 크기 조정 (configs/train_highperf.yaml)
train:
  batch_size: 92  # 줄이기: 64 → 32 → 16
```

#### 2. 학습 중단 후 재개
```bash
# 체크포인트에서 자동 재개
python src/training/train_main.py \
    --config configs/train_highperf.yaml \
    --resume
```

#### 3. 단일 폴드 최적화 오류
```bash
# 단일 폴드 테스트 실행
python src/optimization/test_single_fold_quick.py

# Optuna 최적화 테스트
python src/optimization/test_optuna_single_fold.py
```

### 로그 확인
```bash
# 최신 학습 로그 확인
tail -f logs/$(date +%Y%m%d)/train/*.log

# 최신 최적화 로그 확인  
tail -f logs/optimization/optuna_*.log
```

---

## 🤝 Contributing

1. 팀 저장소를 개인 저장소로 포크
2. 기능 브랜치를 생성 (`git checkout -b feature/기능명`)
3. 변경사항을 커밋 (`git commit -m 'feat: 커밋 내용'`)
4. 브랜치에 푸시 (`git push origin feature/기능명`)
5. Pull Request

---

## 🙏 Acknowledgments

- **ConvNeXt Base 384**: F1 0.96909 달성의 핵심 모델
- **Optuna**: 하이퍼파라미터 최적화 프레임워크  
- **단일 폴드 최적화**: 경진대회를 위한 고속 최적화 전략
- **데이터셋 캐싱**: 매 trial 2초 달성의 핵심 기술

---

## 📞 Contact & Support

- **Issues**: [GitHub Issues](../../issues)
- **Wiki**: [프로젝트 Wiki](../../wiki)  
- **Docs**: `docs/` 폴더 내 상세 문서들