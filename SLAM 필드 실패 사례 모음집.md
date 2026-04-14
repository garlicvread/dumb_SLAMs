# SLAM 필드 실패 실증 보고서: 논문과 현실의 격차

본 문서는 "시각장애인 안내 로봇-코봇 관제 시스템 차이 분석"의 SLAM 부적합성 주장을 뒷받침하는 실증 자료를 정리한 참고자료다. GitHub Issues, 학술 논문, 개발자 포럼에서 수집한 실제 필드 실패 사례를 포함한다.

---

## 1. 기존 주요 SLAM 구현체

### DynaSLAM
- 프레임당 0.5초 처리(2 FPS) — 실시간 대비 최소 5배 느림 (피드백 제어 영역에서는 10Hz 정도 제어주기라면 실시간으로 인정하는 분위기)
- Python 2.7 의존, 빌드 자체가 현대 환경에서 어려움
- 동적 객체 마스크 미생성: [Issue #87](https://github.com/BertaBescos/DynaSLAM/issues/87)
- 추적 초기화 실패: [Issue #90](https://github.com/BertaBescos/DynaSLAM/issues/90)
- Mask R-CNN 로딩 시 즉시 크래시: [Issue #89](https://github.com/BertaBescos/DynaSLAM/issues/89)

### ORB-SLAM3
- 평지 직선 주행에서 IMU 초기화 실패, 유지관리자가 "자동차에는 Stereo 권장": [Issue #435](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/435)
- 급격한 모션 후 추적 복구 불가: [Issue #366](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/366)
- 회전 시 카메라 드리프트: [Issue #150](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/150)
- 항공 야외 환경에서 매 스텝 추적 손실: [Issue #313](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/313)
- IMU 초기화 후 직선 주행 시 정확도 급락: [Issue #356](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/356)
- 커뮤니티 집단 고통 요약: [Issue #736](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/736)

### slam_toolbox
- 동적 창고에서 20분 후 로컬라이제이션 완전 붕괴, 40cm 드리프트: [Issue #334](https://github.com/SteveMacenski/slam_toolbox/issues/334)
- 유지관리자가 동적 환경의 근본적 한계 인정: [Issue #108](https://github.com/SteveMacenski/slam_toolbox/issues/108)

### RTAB-Map
- 동적 환경에서 정확도 저하 보고: [Issue #852](https://github.com/introlab/rtabmap_ros/issues/852). 유지관리자가 별도 ROS Answers 스레드에서 *"RTAB-Map doesn't handle explicitly moving objects"*, *"For /rtabmap/cloud_map, moving objects cannot be cleared"*라고 공식 인정. 보행자가 3D 맵에 영구 장애물로 기록됨

---

## 2. Semantic SLAM 계열

### Kimera (MIT SPARK Lab)
- 루프 클로저(LCD) 활성화 시 즉시 SIGSEGV 크래시: [Issue #209](https://github.com/MIT-SPARK/Kimera-VIO/issues/209)
- Carla 시뮬레이터에서 궤적 출력 전혀 없음: [Issue #231](https://github.com/MIT-SPARK/Kimera-VIO/issues/231)
- Docker 환경에서 빌드 실패: [Issue #219](https://github.com/MIT-SPARK/Kimera-VIO/issues/219)

### DS-SLAM
- "사람(person)"만 동적 처리. 자동차, 자전거, 동물은 정적 취급. 20개 카테고리 외 처리 불가
- DS-SLAM의 야외 한계를 지적한 후속 연구: *"DS-SLAM is not suitable for outdoor use as it only finds the dynamic mask for 'person'"* — 원논문: [arXiv 1809.08379](https://arxiv.org/abs/1809.08379)

### SuMa++ (PRBonn)
- 루프 클로저 자체가 없음 — 장거리 야외에서 드리프트 필연적. 원논문: [arXiv 1905.07626](https://arxiv.org/abs/1905.07626)
- 동적 객체 제거 시 포즈 추정 특징점도 함께 제거되는 역설적 트레이드오프
- 동적 객체 필터링 결함: [Issue #60](https://github.com/PRBonn/semantic_suma/issues/60)
- 시맨틱 분할 오류: [Issue #62](https://github.com/PRBonn/semantic_suma/issues/62)

---

## 3. Learning-based SLAM 계열

### DROID-SLAM (Princeton VL)
- "DROID-SLAM in the Wild"(ETH Zurich, Moyang Li et al.)에서 DROID-SLAM의 야외 동적 환경 실패를 공식 문서화: *"dynamic objects violate the rigid-motion assumption, yielding unreliable residuals that destabilize the BA layer"*: [arXiv 2603.19076](https://arxiv.org/html/2603.19076v1)
- CUDA 에러: [Issue #133](https://github.com/princeton-vl/DROID-SLAM/issues/133)
- 세그멘테이션 폴트: [Issue #131](https://github.com/princeton-vl/DROID-SLAM/issues/131)
- 포인트 클라우드 희소/레이어링: [Issue #135](https://github.com/princeton-vl/DROID-SLAM/issues/135)

---

## 4. LiDAR SLAM 계열

### FAST-LIO2 (HKU MARS Lab)
- 루프 클로저 부재로 드리프트 누적. 루프 클로저 모듈이 설계에 포함되어 있지 않아 장거리 주행 시 오차가 리셋되지 않음
- 주차장 800m 루프에서 4미터 수직 드리프트: [Issue #121](https://github.com/hku-mars/FAST_LIO/issues/121)
- 드론 야외 테스트에서 잦은 위치 추정 실패: [Issue #57](https://github.com/hku-mars/FAST_LIO/issues/57)

### LOAM / A-LOAM / F-LOAM
- 루프 클로저 없음 — 누적 오차 수정 불가. KITTI 1위였지만 드리프트 수정 메커니즘 부재

### LeGO-LOAM
- 지면 평면 가정 — 계단, UAV, 경사면에서 가정 위반으로 불안정
- 솔리드 스테이트 LiDAR(Livox AVIA 등)와 근본적 비호환: [GitHub](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM)

### LIO-SAM
- 야외 차량 테스트에서 "terrible zigzag and jerking": [Issue #238](https://github.com/TixiaoShan/LIO-SAM/issues/238)
- 6축 IMU 미지원 — 저가 보급 센서(Ouster 내장 IMU 등) 호환 불가

---

## 5. Gaussian Splatting SLAM 계열

### SplaTAM (CVPR 2024)
- 소비자 GPU(RTX 4060 8GB)에서 표준 벤치마크(Replica) 1050 스텝 만에 OOM
- CUDA 메모리 부족: [Issue #124](https://github.com/spla-tam/SplaTAM/issues/124)
- 루프 클로저 미구현: [Issue #151](https://github.com/spla-tam/SplaTAM/issues/151)

### MonoGS (CVPR 2024 Best Demo Award)
- 야외 계절 변화 환경 벤치마크에서 단안 모드 기능 제한적이거나 완전 실패: [arXiv 2412.03263](https://arxiv.org/abs/2412.03263) (NeRF/3DGS SLAM 야외 비교 벤치마크)
- 동적 객체 고스팅 아티팩트 심각
- CUDA OOM: [Issue #197](https://github.com/muskie82/MonoGS/issues/197)
- 재구성 정지: [Issue #169](https://github.com/muskie82/MonoGS/issues/169)
- 100개 이상 오픈 이슈: [Issues](https://github.com/muskie82/MonoGS/issues)

### 2024년 최신 방법 종합 실패
- AgriGS-SLAM 논문에서 과수원 환경 벤치마크 시 기존 3DGS-SLAM 베이스라인 대비 심각한 성능 저하 보고: [arXiv 2510.26358](https://arxiv.org/abs/2510.26358)

---

## 6. Event Camera SLAM 계열

### EVO / ESVO / Ultimate SLAM
- HKU 도전적 데이터셋 대부분 시퀀스에서 실패: [GitHub](https://github.com/arclab-hku/Event_based_VO-VIO-SLAM)
- 저속 동작에서 이벤트 부족으로 특징 추적 불안정 — HKU 데이터셋 README에서 *"TS maps exhibit poor quality when there are few events"* 확인: [GitHub README](https://github.com/arclab-hku/Event_based_VO-VIO-SLAM)

---

## 7. Multi-modal SLAM 계열

### R3LIVE (HKU MARS Lab)
- 개발팀 공식 인정: *"We know our packages might not totally stable in this stage"*: [README](https://github.com/hku-mars/r3live/blob/master/README.md)
- 대규모 드리프트 보고: [Issue #45](https://github.com/hku-mars/r3live/issues/45)

---

## 8. Visual Place Recognition 기반

### NetVLAD / Patch-NetVLAD
- AnyLoc 논문(2024)에서 야외 훈련 모델의 비정형 환경 일반화 실패를 지적: *"lack of generalization for outdoor-trained models on indoor and unstructured environments"*: [AnyLoc 논문](https://anyloc.github.io/)
- 장소 인식(place recognition) 자체가 반복 패턴 환경에서 거짓 양성을 생성하는 구조적 한계를 가짐

---

## 9. 악천후 — SLAM 시스템 전반

- CVPR 2024 SubT-MRS: "existing SLAM solutions remain fragile in challenging conditions": [논문](https://openaccess.thecvf.com/content/CVPR2024/papers/Zhao_SubT-MRS_Dataset_Pushing_SLAM_Towards_All-weather_Environments_CVPR_2024_paper.pdf)
- LiDAR 악천후 성능 저하 실측: 포인트 클라우드 수 59% 감소 ([PMC: 악천후 LiDAR 실측](https://pmc.ncbi.nlm.nih.gov/articles/PMC10051412/))

---

## 10. 학술 커뮤니티의 자인

- **ScienceDirect 2024**: "the impressive performance of most state-of-the-art VSLAM algorithms is not generally reflected in unstructured planetary-like and agricultural environments": [논문](https://www.sciencedirect.com/science/article/abs/pii/S016786552400285X)
- **Journal of Field Robotics 2025**: 7개 최신 SLAM(ORB-SLAM3, VINS-Fusion, OpenVINS, Kimera, SVO Pro, HFNet-SLAM, AirSLAM) 모두 비정형 야외에서 제한적 성능: [논문](https://onlinelibrary.wiley.com/doi/10.1002/rob.22581)
- **Advanced Robotics 2024**: "Tracking failure in state-of-the-art visual SLAM has been reported to be frequent and hampers real-world deployment": [DOI: 10.1080/01691864.2024.2319144](https://doi.org/10.1080/01691864.2024.2319144)
- **Sensors 2025**: 시맨틱 SLAM이 "폐쇄형 어휘"에 갇힌 한계: [논문](https://www.mdpi.com/1424-8220/25/16/4994)
- **ROCK Robotic 현장 측량사**: "it doesn't feel as refined and usable as they sell in the videos and marketing": [포럼](https://community.rockrobotic.com/t/ive-got-slam-mertime-sadness/1544)

---

## 11. 정적 환경에서도 실패하는 SLAM

"동적 객체 때문에 SLAM이 안 된다"는 비판은 SLAM에 너무 관대하다. 정적 환경을 줘도 제대로 못 한다.

### IMU 초기화 — 평지 직선 주행에서 구조적 실패
- ORB-SLAM3 공식 인정: *"In applications with slow motions, or without roll and pitch rotations, such as a car in a flat area, IMU sensors can be difficult to initialize."*: [Issue #736](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/736)
- 2025년에도 동일 문제 반복: [Issue #980](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/980)
- 관련 미해결 이슈: [#28](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/28), [#69](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/69), [#148](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/148), [#356](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/356), [#393](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/393), [#435](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/435), [#866](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/866), [#910](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/910), [#933](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/933), [#968](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/968), [#980](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/980)

### 정적 환경 드리프트
- LeGO-LOAM: 정적 실내에서 피처 부족으로 드리프트. 개발팀이 **"wontfix"** 라벨 부착 — 근본 한계로 공식 인정: [Issue #181](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM/issues/181)
- LIO-SAM: 정적 환경에서 하드웨어 무관하게 드리프트. Hesai, Velodyne, 시뮬레이션 모두 동일: [Issue #556](https://github.com/TixiaoShan/LIO-SAM/issues/556)

### 복도/터널 기하학적 퇴화 (완전 정적, 그런데 수십 미터 오차)
- FAST-LIVO2: 정적 건물 복도에서 *"the odometry still drifts by tens of meters"*: [Issue #91](https://github.com/hku-mars/FAST-LIVO2/issues/91)
- 학술 확인: *"In long straight corridors, the lack of orthogonal orientation constraints makes it difficult for the robot to perform accurate localization"*: [arXiv:2502.11486v2](https://arxiv.org/abs/2502.11486)

### 피처 부족 — 흰 벽 앞에서 크래시
- ORB-SLAM3: 빈 벽 방향 시 *"Fail to track local map! NOT ENOUGH EDGES"* 후 **Segmentation fault (core dumped)**: [Issue #365](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/365)
- OpenVINS: *"When camera is facing a white wall (zero features) and not moving, the VIO drifts..."*: [Issue #417](https://github.com/rpng/open_vins/issues/417)
- 관련 이슈: [ORB-SLAM3 #610](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/610), [#232](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/232), [#85](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/85); [VINS-Mono #35](https://github.com/HKUST-Aerial-Robotics/VINS-Mono/issues/35), [#458](https://github.com/HKUST-Aerial-Robotics/VINS-Mono/issues/458)

### 루프 클로저가 오히려 맵을 파괴
- RTAB-Map: 잘못된 루프 클로저가 정확한 맵을 파괴. 유지보수자 인정: *"because the covariance is fixed, it is difficult to reject it after optimization"*: [Issue #1032](https://github.com/introlab/rtabmap_ros/issues/1032)
- 빌드 설정(G2O vs GTSAM)에 따라 루프 클로저 동작 자체가 변함: [Issue #1649](https://github.com/introlab/rtabmap/issues/1649)

### 메모리/연산 폭발 — 표준 벤치마크에서도 OOM
- SplaTAM: RTX 4060 8GB로 Replica 실내 벤치마크 1050 스텝에서 OOM: [Issue #124](https://github.com/spla-tam/SplaTAM/issues/124)
- MonoGS(CVPR 2024 Highlight): KITTI에서 cudaError 즉시 크래시: [Issue #142](https://github.com/muskie82/MonoGS/issues/142)
- GLIM: *"The memory usage grows indefinitely as the map area grows"* — 유지보수자 직접 확인: [Issue #223](https://github.com/koide3/glim/issues/223)

### 단안 스케일 드리프트 — 회전만 해도 누적
- ORB-SLAM3: *"the scale error becomes larger and larger as the turns increase"*, 키프레임이 한 점에 뭉침. 수정 시도 모두 실패, 미해결: [Issue #243](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/243)

### 빌드/의존성 지옥
- ORB-SLAM3: Ubuntu 최신 버전에서 Eigen3 호환성 실패 ([Issue #794](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/794)). 빌드 관련 미해결 이슈 10개+: [#16](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/16), [#18](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/18), [#585](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/585), [#660](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/660), [#742](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/742), [#794](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/794), [#797](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/797), [#903](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/903), [#913](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/913), [#934](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/934)
- slam_toolbox: Ubuntu 24.04 출시 즉시 빌드 불가: [Issue #685](https://github.com/SteveMacenski/slam_toolbox/issues/685)

---

## 12. "SLAM의 한계를 해결했다"는 주장의 검증

### 드리프트 해결?
- **주장**: LIO-SAM, FAST-LIO2 등 LiDAR-IMU 타이트 커플링이 드리프트를 해결
- **현실**: 소규모 실내에서만 관리됨. 수 km 이상에서는 미검증. *"사전 지식 없이는 수 킬로미터 이내에서도 올바른 위치 추정을 보장할 수 없다"*: [ScienceDirect 2022](https://www.sciencedirect.com/science/article/abs/pii/S0952197622001853)
- **판정: 미해결** — "해결"이 아닌 "관리(mitigation)"

### 동적 객체 실시간 처리?
- **주장**: BY-SLAM, NGD-SLAM 등이 동적 객체를 실시간 처리
- **현실**: BY-SLAM 프레임당 48.6~52.3ms(i9 CPU 기준). 원 논문은 이를 "실시간 가능"이라 주장하나, 로봇 온보드 임베디드 프로세서에서는 이 수치 달성이 비현실적 ([PMC: BY-SLAM](https://pmc.ncbi.nlm.nih.gov/articles/PMC11280888/)). *"딥러닝은 정확도는 높으나 컴퓨팅 자원 제약으로 실시간 처리가 어렵다"* ([PMC: ADM-SLAM](https://pmc.ncbi.nlm.nih.gov/articles/PMC11175307/)). 2024년에도 DOT-SLAM, DI-SLAM 등 새 논문이 계속 나오는 것 자체가 미해결 증거
- **판정: 미해결**

### 루프 클로저 거짓 양성?
- **주장**: NetVLAD, LoopGNN 등으로 해결
- **현실**: *"검증 없는 SLAM 시스템은 반복 패턴 유발 거짓 양성으로 엄청난 오차를 보인다"*: [ROVER, arXiv 2025](https://arxiv.org/html/2508.13488). 문제를 검증 단계로 이전했을 뿐
- **판정: 미해결** — 문제 이전(problem displacement)

### 악천후 대응?
- **주장**: LiDAR-Visual 융합으로 해결
- **현실**: 짙은 안개(가시거리 ≤50m)에서 여전히 실패 ([PMC: 악천후 LiDAR 실측](https://pmc.ncbi.nlm.nih.gov/articles/PMC10051412/)). LiDAR 탐지 거리 25% 감소 실측. *"융합은 우회책(workaround)에 불과"*: [arXiv: LiDAR 악천후 서베이](https://arxiv.org/pdf/2304.06312)
- **판정: 미해결**

### 피처 빈약 환경(터널/복도)?
- **주장**: LiDAR-IMU 융합 또는 보조 수단으로 해결
- **현실**: DARPA SubT에서 비주얼 SLAM은 터널 내 거의 모든 팀에서 실패 ([IEEE TRO: SubT 서베이](https://ieeexplore.ieee.org/document/10286080/)). QR 코드 랜드마크 같은 **인프라 의존** 없이는 불안정: [Nature Scientific Reports 2025](https://www.nature.com/articles/s41598-025-34165-2)
- **판정: 인프라 의존 조건부만 가능**

### IMU 초기화?
- **주장**: 개선된 초기화 전략으로 해결
- **현실**: 2026년 현재도 GitHub에서 가장 많이 보고되는 실패 원인. ORB-SLAM3 이슈 [#28](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/28), [#148](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/148), [#910](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/910), [#968](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/968) 등 현재도 활성. 실제 운영 지침이 "충분한 6-DoF 운동으로 시작하라"인 것 자체가 미해결 증거
- **판정: 미해결**

### 단안 스케일 드리프트?
- **주장**: IMU 융합으로 해결
- **현실**: ORB-SLAM3 공식 — 평탄한 도로 직선 주행 시 IMU 초기화 어려움. 수 km 규모에서의 독립 검증 없음
- **판정: 조건부, 대규모 미해결**

### 실제로 해결된 것
- **실내 2D LiDAR SLAM (창고/공장 AGV)**: ±10-20mm 정확도. 전 세계 수천 개 상업 배포. 사례: [LSROBOT 2024 Shenzhen Robotics Application Case Award](https://www.leishenrobot.com/lsrobot-pioneering-a-new-era-of-intelligent-manufacturing-wins-2024-shenzhen-robotics-application-case-award/), [Yujin Robot AMR SLAM 적용](https://yujinrobot.com/en/resource/blog/blog/how-slam-and-3d-lidar-solve-for-amr-technology). 조건: 정적/느리게 변하는 실내, 단층, LiDAR 가시거리 내 구조물. **이 조건 바깥에서는 미해결.**
- **DARPA SubT (지하 극한환경)**: 6팀 3년 검증. 1위 CERBERUS 23/40점 ([DARPA 공식 발표](https://www.darpa.mil/news/2021/subterranean-challenge-winners)). 기술 상세: [CERBERUS Science Robotics 논문](https://www.science.org/doi/10.1126/scirobotics.abp9742), [CMU Team Explorer 4위 결과](https://www.cmu.edu/news/stories/archives/2021/september/darpa-subt-finals.html). **실질 작동하나 완전 자율은 아님** — 원격 감시 + 부분 인간 개입 허용.

---

## 13. SLAM 옹호론과 그 반박 — 종국적으로 끝난 논쟁

### 논쟁 1: "피처 없는 환경은 해결 가능하다"
- **옹호**: LiDAR-IMU 융합으로 피처 빈약 환경 대응 가능
- **반박**: Boston Dynamics가 공식 SDK 문서에서 직접 인정: *"every position on a snow-covered field looks the same — there isn't enough detail for localization to succeed"*: [공식 문서](https://dev.bostondynamics.com/docs/concepts/autonomy/localization)
- **종결 여부: 제조사 자인으로 사실상 종결.** SLAM 방식으로 피처 없는 환경은 근본적으로 불가능하다는 것을 당사자가 인정.

### 논쟁 2: "Dynamic SLAM이 동적 환경을 해결했다"
- **옹호**: DynaSLAM, Semantic SLAM으로 동적 객체 제거 가능
- **반박**: RTAB-Map 유지관리자가 *"doesn't handle explicitly moving objects"*라고 공식 인정 (ROS Answers). DynaSLAM은 0.5초/프레임으로 실시간 불가 ([Issue #87](https://github.com/BertaBescos/DynaSLAM/issues/87)). DROID-SLAM의 야외 동적 환경 실패가 후속 연구에서 공식 문서화 ([arXiv 2603.19076](https://arxiv.org/html/2603.19076v1)).
- **종결 여부: 사실상 종결.** 주요 구현체 유지관리자들과 원저자들이 직접 한계를 인정. 실시간 + 실외 동적 환경에서 작동하는 SLAM은 2026년 현재 존재하지 않음.

### 논쟁 3: "대규모 실외에서 SLAM 드리프트는 해결 가능하다"
- **옹호**: 루프 클로저 + IMU 융합으로 드리프트 관리
- **반박**: FAST-LIO2 — 정적 주차장 800m에서 4m 수직 드리프트 ([Issue #121](https://github.com/hku-mars/FAST_LIO/issues/121)). LeGO-LOAM 개발팀 "wontfix" ([Issue #181](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM/issues/181)). LIO-SAM 원 논문(MIT)에서 *"루프 클로저 없이 장거리 주행에서 점진적 오차 축적 지속"* 인정: [LIO-SAM 논문](https://senseable.mit.edu/papers/pdf/20201020_Shan-etal_LIO-SAM_IROS.pdf)
- **종결 여부: 종결.** 루프 클로저 없이는 드리프트가 불가피하며, 루프 클로저 자체가 거짓 양성/맵 파괴 문제를 가짐. GPS 보정 없이 수십 km를 오차 보장하는 시스템 없음.

### 논쟁 4: "Starship/배달로봇이 SLAM으로 자율주행에 성공했다"
- **옹호**: Starship 9백만 건 배달, 99% 자율성
- **반박**: "99% 자율성" 계산 방법 비공개 ([Robot Report](https://www.therobotreport.com/starship-technologies-surpasses-8m-autonomous-deliveries/)). 인간 원격 지원자 상시 대기. 사용 기술이 순수 SLAM인지 GPS+딥러닝 네비게이션 융합인지 불명확. Waymo도 "SLAM 사용"이라 불리지만 실제로는 사전 HD맵 기반 로컬라이제이션: [Waymo 매핑 블로그](https://waymo.com/blog/2020/09/the-waymo-driver-handbook-mapping/)
- **종결 여부: 미종결, 증거 불충분.** "SLAM으로 자율주행 성공"이라는 주장의 독립 검증이 존재하지 않음. 기업 자체 주장만 있음.

### 논쟁 5: "SLAM은 죽지 않았다 — 실내/지하에서 작동한다"
- **옹호**: 창고 AGV ±10-20mm, DARPA SubT 검증 ([DARPA 발표](https://www.darpa.mil/news/2021/subterranean-challenge-winners), [Science Robotics 논문](https://www.science.org/doi/10.1126/scirobotics.abp9742)), Emesent 광산 SLAM ([Emesent 제품페이지](https://www.emesent.com/emesent-product/hovermap-series/))
- **반박 없음**: 이것은 사실이다. 구조화된 실내, 반구조적 지하 환경에서 SLAM은 실용적으로 작동한다.
- **종결 여부: 양측 합의로 종결.** SLAM은 구조화된/반구조적 환경에서 작동한다. 그러나 이것은 "비정형 야외 보도에서 자율주행"과는 완전히 다른 문제다.

---

## 14. 최종 요약: SLAM이 작동하는 곳과 작동하지 않는 곳

| 환경 | SLAM 신뢰성 | 증거 수준 | 비고 |
|------|-----------|---------|------|
| 구조화된 실내 (창고/공장) | **작동함** | 상업 배포 수천 건 | ±10-20mm, 실용화 완료 |
| 반구조적 지하 (광산/터널) | **작동함** | DARPA 독립 검증 | Emesent 10mm, ANYbotics 배포 |
| 산업 시설 야외 (파이프/구조물) | **조건부** | ANYbotics 배포 | 반구조적, 특징 풍부 |
| 도시 보도 | **불확실** | Starship 주장 (미검증) | 순수 SLAM인지 불명확 |
| 비정형 야외 자연환경 | **실패** | 학술 합의 (ScienceDirect 2024) | 계절/날씨/식생 변화에 취약 |
| 피처 없는 환경 (눈밭/빈 들판) | **불가능** | Boston Dynamics 공식 인정 | "localization impossible" |
| 악천후 (짙은 안개/폭우) | **실패** | 실측 데이터 (PMC 2023) | LiDAR -25%, 카메라 -50% |
| 정적이지만 피처 빈약 (복도/흰 벽) | **실패** | 다수 GitHub Issues | wontfix, segfault, 수십m 드리프트 |

---

## 15. "논문대로 했는데 안 된다" vs "니가 잘못한 거다" — GitHub 이슈 스레드 전수 추적

SLAM 이슈에서 반복되는 패턴: 사용자가 "안 된다"고 보고하면 메인테이너나 커뮤니티가 "설정이 잘못된 거다"라고 반박한다. 그 반박이 실제로 맞았는지 추적한 결과:

### 반박이 유효했던 경우 (실제 사용자 오류)

**LIO-SAM [Issue #238](https://github.com/TixiaoShan/LIO-SAM/issues/238)**: IMU를 LiDAR 회전부 바로 옆에 장착해서 진동 간섭 발생. 캘리브레이션 개선 후 커뮤니티 사용자(lincob)가 *"the improvement in terms of jerking is quite noticeable"*이라고 확인. 단, **원 질문자(fergcd)는 해결 확인 안 함.** Stale bot 자동 종료.

**ORB-SLAM3 [Issue #736](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/736)**: 캘리브레이션 미비, 타임스탬프 오프셋 미보정 등이 실제 원인인 경우 있음. 작성자 본인은 *"The above solutions solves my problem"*이라고 확인. **단, 전제 조건이 있음**: 충분한 roll/pitch 회전이 있는 환경이어야 함. 평지 직선 주행에서는 파라미터 튜닝으로 해결 불가 — 이건 설계 한계.

### 반박이 무효했던 경우 (진짜 알고리즘 한계)

**ORB-SLAM3 [Issue #435](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/435)**: 메인테이너(jdtardos, MEMBER)가 *"For a car we recommend using stereo"* 라고 반박. 실제로 Stereo로 바꿔봤더니? 7개월 후 사용자(makolele12)가 직접 검증: *"I've found out that the map gets initialized longer, but it eventually fails after a while."* → **권장된 해결책도 실패.**

**ORB-SLAM3 [Issue #366](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/366)**: 급격한 모션 후 드리프트 지속. **메인테이너 응답 자체가 없음.** 다른 사용자(mateosss): *"I have encountered similar issues with both mono-inertial and stereo-inertial modes"*. 8개월 후 *"do you solve the problem?"* — 응답 없음. 이슈 Open 상태.

**ORB-SLAM3 [Issue #365](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues/365)**: 흰 벽 앞 segfault. **메인테이너 응답 없음.** 2년간 *"Did you find the cause of this error?"* 질문만 쌓임. 이슈 Open 상태.

**slam_toolbox [Issue #334](https://github.com/SteveMacenski/slam_toolbox/issues/334)**: 메인테이너(SteveMacenski)가 HuberLoss 임시방편을 제안하면서 **스스로 인정**: *"I fully recognize whenever the loss function 'fixes' an issue, that means there's another real issue to be fixed and that its just doing a good job at masking it."* → 메인테이너가 임시방편임을 직접 인정. 2024년까지 Open, 근본 미해결.

**RTAB-Map [Issue #852](https://github.com/introlab/rtabmap_ros/issues/852)**: 메인테이너(matlabbe)가 외부 도구([dotmask](https://github.com/introlab/dotmask)) 사용을 제안. **효과 미확인** — 사용자가 dotmask 시도 결과를 보고하지 않음. 메인테이너 본인도 *"I am currently working on some metrics right now for a company. It is a work on progress, so I cannot really share a lot..."*이라며 해결이 진행 중(=아직 안 됨)임을 인정.

### 메인테이너 완전 방치 (응답조차 없음)

**DynaSLAM [Issue #87](https://github.com/BertaBescos/DynaSLAM/issues/87)**: 검은 마스크 출력. 메인테이너 응답 없음. 2022년 제기 → 2025년 5월 *"how did you solve it?"* → 응답 없음. **3년째 방치.**

**DynaSLAM [Issue #89](https://github.com/BertaBescos/DynaSLAM/issues/89)**: Mask R-CNN segfault. **댓글 0개.** 완전 방치.

### Stale bot이 "해결"을 가장하는 패턴

**FAST-LIO2 [Issue #121](https://github.com/hku-mars/FAST_LIO/issues/121)**: 주차장 4m 드리프트. 팀(Joanna-HE, MEMBER)이 *"try to decrease the noise of imu, and increase the noise of laser measurement"* 제안. **사용자 확인 없음.** 다른 사용자들이 *"Were you able to resolve the z-drift problem?"* → 응답 없음. **Stale bot이 비활성으로 자동 종료 → "completed" 표시.** 해결 여부 불명.

**SplaTAM [Issue #124](https://github.com/spla-tam/SplaTAM/issues/124)**: CUDA OOM. 메인테이너(Nik-V9)가 *"you can run the reconstruction at a lower resolution"* 제안. 다른 사용자(czeyveli1): *"I cannot understand how they rendered the Replica Dataset using 3080 Ti."* → 논문에 VRAM 요구사항 미명시. **해결이 아닌 품질 포기.**

### 요약 패턴

| 패턴 | 건수 | 의미 |
|------|------|------|
| 반박 유효 (사용자 오류) | 2/11 | 캘리브레이션/장착 문제 |
| 반박 무효 (알고리즘 한계) | 5/11 | 권장 수정도 실패, 메인테이너 인정 |
| 메인테이너 무응답 | 3/11 | 알려진 한계의 간접 인정 |
| Stale bot 가짜 해결 | 2/11 (중복) | 확인 없이 자동 종료 |

**11건 중 사용자 오류가 진짜 원인인 경우는 2건뿐이며, 그마저도 "특정 조건에서만" 유효하다.** 나머지 9건은 알고리즘의 근본 한계이거나 메인테이너가 응답을 포기한 경우다.

---

**결론**: SLAM을 활용하여 비정형 야외 환경에서 자율주행을 한다는 주장은, 현존하는 모든 실증 증거에 의해 반박된다. SLAM이 작동하는 영역(구조화된 실내, 반구조적 지하)과 작동하지 않는 영역(비정형 야외)은 명확히 구분되며, 이 경계를 넘는 데 성공한 독립 검증 사례는 2026년 현재 존재하지 않는다.
