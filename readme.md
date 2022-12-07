# Nextflow
### <br/><br/><br/>

## Nextflow 란?
### Bioinfomatics 에서 많이 쓰이는 workflow language 중 하나 이다.
### workflow language 로 주로 쓰이는 언어는 크게 3 가지가 있다.
- snakemake
- WDL
- nextflow
### <br/><br/><br/>

## 워크플로우 언어를 쓰는 이유
### 아무래도 생물정보학 쪽에는 베이스가 생명과학이기도 하고 프로그래밍에 강한 사람이 드물기 때문에 간단하게 분석 파이프라인을 만들 수 있어서인 것이 가장 큰 이유이지 않을까 싶다.
### <br/><br/><br/>

## 워크플로우 언어의 단점
##### 코드가 깔끔해진다고 하는데 input -> output 관계가 복잡해지고 많아지면 오히려 관리가 더 힘들어진다.
##### 오히려 워크플로우 언어는 input -> output 으로만 동작하는 컨셉에서 벗어나지 못 하기 때문에 한계점이 너무 명확해서 불편하다. 
##### 다양한 방식으로 예외 처리를 못 한다. 간혹가다가 워크플로우 언어의 문제로 에러가 발생한 것이라면 왜 에러가 발생하였는지 정말 알아내기가 어렵다. 
##### 그 예로는 snakemake 에서 분명 환경 전달이 잘 된 것 같은데 환경이 달라졌다고 나온다. conda info 로 환경을 확인해보면 잘 전달 되었다고 나온다. 그런데 conda 안에 있는 실행되어야 할 프로그램이 default 경로에 있는 프로그램이 실행이 되는 경우가 있다(?).
##### 결국 한계점을 해결하기 위해 프로그래밍 언어를 동반해서 써야 하기 때문에 언어 문법을 2가지 이상 알아야 한다.
### <br/><br/><br/>

## snakemake 의 한계
### 먼저 서버 간 클러스터링을 하거나 다중 작업을 제출할 때 환경이 다 깨지는 문제가 발생한다.
### 여러 번의 테스트와 오랜 시간을 들여 찾은 결과 왜 이런 문제가 발생하는지 찾았고, 많은 문제점을 발견하였다.
- 구조가 하나의 input -> output 을 생산하는 구조이다. 이 하나하나를 rule 이라고 한다.
- rule 을 하나씩 실행할 때마다 새로운 쉘을 열어서 명령어를 실행한다.
- 같은 서버 내에서라도 새로운 쉘을 열 때마다 그 쉘에서 상속받지 못 하는 환경 변수가 신기하게도 생긴다.
- 환경 변수를 전달 받지 못 하는 문제로 에러가 생긴다.
- SGE (Sun Grid Engine) 으로 묶인 queue server 로 qsub 명령어를 통해 서버 클러스터링을 하는 옵션을 지원해주긴 하지만, 호스트 서버의 환경도 해결하지 못 한 상태라서 다른 서버는 더욱이나 적용이 어렵다.
- 그래서 singularity, docker 를 사용하여 환경 문제를 해결하긴 한다. 
- 매번 실행 때마다 새로운 환경(같은 환경을...)을 다운로드 받는다. 용량적으로 굉장히 디스어드벤티지다.
- log 관리가 어렵다.
- input -> output 구조이기 때문에 수많은 rule 이 생긴다. 그러면서 rule 이 수십개 생기는 경우가 다반사다. 관리가 어려워진다.
- 속도 : 매우매우 느리다. 프로그램 자체가 많이 무겁다.
### <br/><br/><br/>

## nextflow 언어
### seqera 라는 회사에서 만들었다.
### seqera 의 파트너
- AWS
- google
- Azure
### 생물정보학이라는 특정 전문 집단에서만 많이 쓰이기 때문에 확실히 점유율은 적다(사실 workflow language 자체를 잘 쓰지 않는다).
#### ![image](https://user-images.githubusercontent.com/62974484/206093341-41071e63-fe14-4cd1-aa40-bc9a023a9c40.png)
### google cloud 에서는 생물정보학을 위해 Cloud Life Sciences 솔루션 프로젝트를 만들었는데 거기서 지원을 하는 언어이다.
### 분석을 모니터링, 작업 제출을 위해 구축된 웹서버가 있다. nextflow TOWER 라고 한다.
#### ![image](https://user-images.githubusercontent.com/62974484/206093209-b1863cce-ffd2-40f2-8a3b-99144c849a22.png)
### 기본적으로는 회사 (seqera)에서 만든 언어이기 때문에 지원은 잘 된다.
### seqera 의 파트너로 AWS 가 있어 AWS 클라우드와 호환이 된다.
#### https://www.nextflow.io/docs/latest/awscloud.html
### <br/><br/><br/>

## 사용 방법
### 공식 홈페이지의 getting started 를 참고한다.
#### https://www.nextflow.io/
#### ![image](https://user-images.githubusercontent.com/62974484/206094813-a2053603-da98-405f-aa4f-34fb24024829.png)
### 1. install 명령어를 저장해준다.
```
$ curl -s https://get.nextflow.io > nextflow_install.sh
```
### 2. bash nextflow_install.sh 로 실행한다.
### 만약 java version 때문에 에러가 난다면 따로 java 를 설치해준 후 아래와 같이 JAVA_HOME 을 수정해주고 실행한다.
```
# jdk 17 다운로드
$ wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz

# 압축 풀기
$ tar -zxvf jdk-17_linux-x64_bin.tar.gz
```
#### ![image](https://user-images.githubusercontent.com/62974484/206095571-c50c3399-6944-4352-bce8-b1ab556cfc8c.png)
#### ![image](https://user-images.githubusercontent.com/62974484/206095075-8d3b0416-36bf-464e-a6ee-1fd650497728.png)
### 다음과 같이 nextflow 가 생성된다.
#### ![image](https://user-images.githubusercontent.com/62974484/206095828-4d2f6a62-e01e-4e9a-ba6e-2bc0f4a6f2d5.png)
### <br/>
### 3. hello world 를 실행해본다.
```
$ ./nextflow run hello
```
#### ![image](https://user-images.githubusercontent.com/62974484/206095871-e5e04713-0c3c-408b-82f1-b77b07e9b6d1.png)
