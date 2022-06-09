---
title: nvidia/deeops 라이브러리를 이용한 모니터링(prometheus, grafana) 기본 구성
author: Develiberta
date: 2022-01-20 11:00:00 +0900
categories: [DevOps, Ansible]
tags: [DevOps, Ansible]
---


## 학습 목표
---
1. deepops가 무엇인지 이해하고 설명할 수 있다.
2. deepops를 이용해서 기본적인 환경을 구성할 수 있다.

## deepops 개요
---
1. https://github.com/NVIDIA/deepops
2. GPU 인프라 및 자동화 도구

## deepops를 이용한 기본적인 환경 구성
---
1. Install a supported operating system on all nodes.

2. Set up your provisioning machine.
	```shell
	# Install software prerequisites and copy default configuration
	./scripts/setup.sh
	```

3. Create and edit the Ansible inventory.
	```shell
	# Edit inventory
	# Add Slurm controller/login host to `slurm-master` group
	# Add Slurm worker/compute hosts to the `slurm-node` groups
	vi config/inventory
	```
	1. [all] 하위에 ansible을 이용할 모든 노드 기재
	2. [slurm-master] 하위에 master 노드 기재
	3. [slurm-node] 하위에 worker 노드 기재
	4. [all:vars] 하위 #SSH User에 ansible_user 변경

4. (optional) Modify `config/group_vars/*.yml` to set configuration parameters
	```shell
	vi config/group_vars/slurm_cluster.yml
	# 전체 파일을 살펴보며 설정 정보 수정
	```

5. Verify the configuration.
	```shell
	ansible all -m raw -a "hostname" -k
	```

6. (optional) Modify `playbooks/*.yml` to set configuration tasks
	```shell
	vi playbooks/slurm-cluster.yml
	# 전체 파일을 살펴보며 설정 정보 수정
	```

7. Install Slurm.
	```shell
	# NOTE: If SSH requires a password, add: `-k`
	# NOTE: If sudo on remote machine requires a password, add: `-K`
	# NOTE: If SSH user is different than current user, add: `-u ubuntu`
	ansible-playbook --ask-become-pass -k -l slurm-cluster playbooks/slurm-cluster.yml
	```

## 참고
1. Slurm Deployment Guide
	https://github.com/NVIDIA/deepops/tree/master/docs/slurm-cluster