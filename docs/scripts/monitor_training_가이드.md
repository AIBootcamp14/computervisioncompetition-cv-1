# 📊 monitor_training.sh 가이드

## 개요
학습 프로세스를 실시간으로 모니터링하고 진행 상황을 확인하는 스크립트입니다.

## 주요 기능
- 🔍 **프로세스 모니터링**: 현재 실행 중인 모든 학습 프로세스 확인
- 📈 **리소스 사용량**: CPU, 메모리 사용률 실시간 표시
- 📝 **로그 추적**: 최신 로그 파일의 마지막 5줄 출력
- ⏰ **실행 시간**: 각 프로세스의 누적 실행 시간 표시

## 사용법

### 기본 실행
```bash
# 프로젝트 루트에서 실행
./scripts/monitor_training.sh
```

### 출력 예시
```
=== 학습 프로세스 모니터링 ===
현재 시간: 2025-09-08 00:53:15

📊 실행 중인 학습 프로세스:
  PID: 1596670 | CPU: 102% | MEM: 7.9% | TIME: 387:35
  PID: 1723807 | CPU: 2.0% | MEM: 4.0% | TIME: 0:04

📝 최신 로그 파일:
  파일: logs/train/train_highperf_20250907-1825_swin-sighperf-d12239_basic_augmentation.log
  마지막 업데이트: 2025-09-08 00:49:58.493492287 +0900

📋 최근 로그 (마지막 5줄):
2025-09-08 00:49:43 | [EPOCH 7] >>> TRAIN start | steps=20 mixup=True
2025-09-08 00:49:58 | [EPOCH 7][TRAIN step 1/20] loss=0.27779 lr=0.000086 bs=64
```

## 모니터링 정보 해석

### 프로세스 정보
- **PID**: 프로세스 고유 식별번호
- **CPU**: CPU 사용률 (100% = 1개 코어 100% 사용)
- **MEM**: 메모리 사용률 (전체 시스템 메모리 대비 %)
- **TIME**: 누적 실행 시간 (시간:분 또는 분:초)

### 로그 정보
- **최신 로그 파일**: 가장 최근에 업데이트된 로그 파일 경로
- **마지막 업데이트**: 로그 파일의 최종 수정 시간
- **최근 로그**: 진행 상황을 파악할 수 있는 마지막 5줄

## 활용 시나리오

### 1. 학습 진행 상황 확인
```bash
# 5분마다 자동 모니터링
while true; do
    ./scripts/monitor_training.sh
    echo "다음 확인까지 5분 대기..."
    sleep 300
done
```

### 2. 프로세스 이상 감지
- **CPU 사용률이 0%**: 프로세스가 멈춘 상태 (에러 가능성)
- **메모리 사용률이 90% 이상**: 메모리 부족 위험
- **TIME이 너무 오래**: 예상 시간을 초과한 실행

### 3. 로그 분석
```bash
# 특정 로그 파일 전체 확인
tail -f logs/train/train_highperf_20250907-1825_swin-sighperf-d12239_basic_augmentation.log

# 에러 로그 검색
grep -i "error\|exception\|fail" logs/train/*.log
```

## 문제 해결

### 프로세스가 보이지 않는 경우
```bash
# 모든 Python 프로세스 확인
ps aux | grep python

# 특정 포트 사용 중인 프로세스 확인 (WandB 등)
netstat -tlnp | grep :8080
```

### 로그 파일이 없는 경우
```bash
# logs 디렉터리 구조 확인
find logs/ -type f -name "*.log" | head -10

# 최근 생성된 파일 확인
find logs/ -type f -mtime -1
```

### 프로세스 종료가 필요한 경우
```bash
# 모든 학습 프로세스 종료
pkill -f train_main.py

# 특정 PID 종료
kill -9 [PID번호]

# 강제 종료 후 GPU 메모리 정리
nvidia-smi --gpu-reset
```

## 자동화 팁

### cron을 이용한 주기적 모니터링
```bash
# crontab 편집
crontab -e

# 매 5분마다 모니터링 결과를 로그 파일에 저장
*/5 * * * * cd /path/to/project && ./scripts/monitor_training.sh >> monitoring.log 2>&1
```

### 알람 설정
```bash
# 프로세스가 종료되면 알림
while ps -p [PID] > /dev/null; do sleep 60; done && echo "학습 완료!" | mail -s "Training Finished" user@email.com
```

## 관련 파일
- `logs/train/`: 학습 로그 디렉터리
- `logs/pipeline/`: 파이프라인 로그 디렉터리
- `src/training/train_main.py`: 메인 학습 스크립트
- `configs/`: 설정 파일들

## 참고 명령어
```bash
# 시스템 리소스 실시간 모니터링
htop

# GPU 모니터링
nvidia-smi -l 5

# 디스크 사용량 확인
df -h

# 네트워크 연결 상태
netstat -an | grep ESTABLISHED
```
