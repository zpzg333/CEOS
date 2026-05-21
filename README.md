# CEOS

> Arista cEOS 컨테이너 이미지 빌드 및 GitHub Container Registry(GHCR) 배포.  
> 홈랩 가상 네트워크 랩에서 사용하는 커스텀 cEOS 이미지를 관리합니다.

---

## 개요

Arista cEOS(Containerized EOS)는 실제 Arista 스위치 OS를 컨테이너로 실행할 수 있게 해주는 이미지입니다.  
이 레포지토리는 cEOS 이미지를 **GHCR(GitHub Container Registry)** 에 배포하고,  
[C9S-containerLAB](../C9S-containerLAB)에서 이 이미지를 활용하여 가상 네트워크 토폴로지를 구성합니다.

## 이미지 정보

| 항목 | 내용 |
|---|---|
| 이미지 | `ghcr.io/zpzg333/ceos` |
| 버전 | 4.34.4F |
| 레지스트리 | GitHub Container Registry (GHCR) |
| 용도 | containerlab / Clabernetes 가상 랩 |

## 이미지 사용 방법

```bash
# GHCR 로그인
echo $GITHUB_TOKEN | docker login ghcr.io -u zpzg333 --password-stdin

# 이미지 Pull
docker pull ghcr.io/zpzg333/ceos:4.34.4F

# cEOS 단독 실행 예시
docker run -it --privileged \
  -e CLAB_MGMT_VRF=MGMT \
  ghcr.io/zpzg333/ceos:4.34.4F \
  Cli
```

## 연관 프로젝트

- [C9S-containerLAB](../C9S-containerLAB) — 이 이미지를 사용하는 EVPN 가상 랩
- [Ansible](../Ansible) — cEOS 인스턴스 자동 상태 점검

## 기술 스택

| 항목 | 기술 |
|---|---|
| 컨테이너 런타임 | Docker |
| 이미지 레지스트리 | GHCR (GitHub Container Registry) |
| 네트워크 OS | Arista EOS 4.34.4F |

## ArgoCD 연동

이 레포지토리의 이미지를 사용하는 **연관 프로젝트들은 모두 ArgoCD와 연동**되어 있습니다.  
GHCR에 새 cEOS 이미지 태그가 push되면, 연관 레포의 YAML에서 태그를 변경하고 GitHub에 push하는 것만으로  
ArgoCD가 자동으로 감지하여 새 이미지를 클러스터에 배포합니다.

```
새 cEOS 이미지 → GHCR push
        ↓
연관 레포 YAML 이미지 태그 변경 → GitHub push
        ↓
ArgoCD 자동 감지 (auto-sync)
        ↓
Clabernetes Pod 새 이미지로 자동 재배포
```

| 항목 | 내용 |
|---|---|
| GitOps 도구 | ArgoCD |
| 배포 트리거 | 연관 레포 GitHub push |
| 이미지 소스 | `ghcr.io/zpzg333/ceos:4.34.4F` |
| ArgoCD 서비스 | www.syargocd.com |

## 배경

Arista cEOS를 GHCR에 호스팅하여 Kubernetes 기반 랩 환경에서  
Private Registry 인증을 통해 안전하게 이미지를 배포하기 위한 프로젝트입니다.
