---
layout: post
title:  암호화
description: Sin tantum est boni praeter summam voluptatem, et eam sempiternam. Cur post Tarentum ad Archytam. Qua ex cognitione facilior facta est investigatio rerum occultissimarum, empull.
date:   2022-05-30 17:36 +0300
image:  '/images/14.jpg'
tags:   [argon2,bcrypt,pbkdf2,scrypt,보안,비밀번호,암호화,패스워드]
---

There’s always a lot of debate in regards to how to safely store passwords and what algorithm to use: MD5, SHA1, SHA256, PBKDF2, Bcrypt, Scrypt, Argon2, plaintext??

## 1. 해시 함수란?

임의의 길이의 데이터를 고정된 길이의 데이터로 반환시켜주는 함수에요.
입력값의 길이가 달라도 출력값은 언제나 고정된 길이로 반환합니다. 
또한 동일한 값이 입력되면 언제나 동일한 출력 값을 보장해요.

## 2. 단방향 해시함수

### 레인보우 테이블 공격(Rainbow Table Attack) 에 취약해요.
현재 패스워드는 '패스워드 + 해시'를 통해 저장되어 있어요.
따라서 패스워드별로 해당 해시값을 미리 저장한 이후 해당 해시값을 통해 역으로 패스워드를 찾아내는 방식이에요.
R 함수는 레인보우 테이블을 작은 크기로 줄이는 데 사용되며 일정한 패턴이나 유사한 것들을 이용하여 모든 값을 저장하는 것이 아닌 특정 값만 저장하여 패스워드를 역으로 알아내는 것입니다.
해시함수는 처리 속도가 빠르기 때문에 Rainbow Table Attack에 노출됩니다.

Key Derivation Functions
키 유도 함수는 솔팅과 키 스트레칭으로 단방향 해시함수의 취약점을 보완한다.
다이제스트를 생성할 때 솔팅과 키 스트레칭을 반복하며 솔트와 패스워드 외에도 입력 값을 추가하여 공격자가 쉽게 다이제스트를 유추할 수 없도록 하고 보안의 강도를 선택할 수 있다.
이 함수들은 GPU와 같은 장비를 이용한 병렬화를 어렵게 하는 기능을 제공한다. 이와 같은 기능은 프로그램이 언어에서 제공하는 라이브러리만으로는 구현하기 어렵다.
대표적인 Key Derivation Function
- PBKDF2
- scrypt
- bcrypt
- argon2 (떠오르는 KDF)

PBKDF2
Password-Based Key Derivation Function
해시 함수의 컨테이너인 PBKDF2는 솔트를 적용한 후 해시 함수의 반복 횟수를 임의로 선택할 수 있다.
PBKDF2는 아주 가볍고 구현하기 쉬우며, SHA와 같이 검증된 해시 함수만을 사용한다.
PBKDF2의 기본 파라미터는 다음과 같은 5개 파라미터다.

DIGEST = PBKDF2(PRF, Password, Salt, c, DLen)  
PRF: 난수(예: HMAC)
Password: 패스워드
Salt: 암호학 솔트
c: 원하는 iteration 반복 수
DLen: 원하는 다이제스트 길이
Scrypt
2009년에 발표된 키 파생 기능 (KDF) 함수이다.
오프라인 brute force 공격에 대해 더 강력하지만, 많은 메모리와 CPU를 사용한다.

다이제스트 생성시 메모리 오버헤드를 갖도록 설계
하드웨어 구현을 하는데 크기와 비용이 훨씬 더 비싸기 때문에, 주어진 자원에서 공격자가 사용할 수 있는 병렬처리의 양이 한정적이다.

OpenSSL 1.1 이상을 제공하는 시스템에서만 작동한다.

여러 언어의 라이브러리로 제공된다.

scrypt의 파라미터는 다음과 같은 6개 파라미터다.

DIGEST = scrypt(Password, Salt, N, r, p, DLen)  
Password: 패스워드
Salt: 암호학 솔트
N: CPU 비용
r: 메모리 비용
p: 병렬화(parallelization)
DLen: 원하는 다이제스트 길이
Bcrypt
1999년에 발표된 blowfish 암호 기반의 암호화 해시 함수이다. - 패스워드 저장을 목적으로 설계
gensalt()의 "work factor"를 조정하는 것만으로 간단하게 시스템의 보안성을 높일 수 있다.
Blowfish 암호를 기반으로 설계된 암호화 함수이며 현재까지 사용중인 가장 강력한 해시 메커니즘 중 하나이다.

보안에 집착하기로 유명한 OpenBSD에서 사용하고 있다. (컴퓨터 보안에 특화된 오픈 소스 운영 체제)

Salting과 Key stretching으로 Rainbow table attack, brute-force 공격에 대비할 수 있다.

C, Java를 포함한 많은 플랫폼, 언어에서 사용할 수 있다.

다만 PBKDF2나 scrypt와는 달리 bcrypt는 입력 값으로 72 bytes character를 사용해야 하는 제약이 있다.

blowfish 암호란?
키(key) 방식의 대칭형 블록 암호
일반 목적으로 사용가능한 비특허 알고리즘
처리 시간과 자원 소비가 최적화되어 각종 온라인 게임의 패킷 암호화, 패킷 복호화에 주로 사용된다.
빠른 속도 덕분에 스마트폰 플랫폼에도 적합하다. 키가 자주 변경되지 않을때 최적의 성능을 보여준다.
스마트폰 메모리에서 서브키를 훔치면 취약할 수 있다.

1. SHA256 / 512  
SHA(Secure Hash Algorithm) 함수는 미국 국가안보국(NSA)이 1993년에 처음으로 설계, 미국 국가 표준으로 지정 SHA-224, SHA-256, SHA-384, SHA-512가 존재, SHA-0과 SHA-1이 있으나 폐기. 최근 SHA-2 함수군에도 SHA-1과 동일한 문제가 있다고 알려져 있다.

2. PBKDF2(Password-Based Key Derivation Function)  
PBKDF2는 NIST(National Institute of Standards and Technology, 미국표준기술연구소)에 의해서 승인된 알고리즘이고, 미국 정부 시스템에서도 사용자 패스워드의 암호화된 다이제스트를 생성할 때 사용한다.

해쉬 컨테이너 알고리즘
사람이 기억할 수 있는 암호 기반으로 랜덤키 생성
입력한 암호 기반으로 salt를 추가 정해진 횟수 만큼 hash 수행
BCrypt
bcrypt는 Blowfish 암호에 따라 Niels Provos 및 DavidMazières에 의해 디자인 된 암호 해시 함수로 1999 년에 USENIX에서 발표되었습니다. 레인보우 테이블 공격으로부터 보호하기 위해 salt를 이용할 뿐만 아니라, 시간이 지남에 따라 반복 횟수를 늘려 늦출 수 있기 때문에 계산 능력이 증가해도 공격에 대한 내성을 유지할 수 있습니다.

bcrypt 함수는 OpenBSD 및 SUSE Linux 등의 일부 Linux 배포판을 포함하여 다른 시스템의 기본 암호 해시 알고리즘.

C, C ++, C #,Elixir, Go, Java, JavaScript, Perl, PHP, Python, Ruby 및 기타 언어에 대한 bcrypt 구현 수 있습니다.

SCrypt
PBKDF2와 유사한 Adaptive Key Derivation Function이며 Colin Percival이 2012년 9월 17일에 설계했다. 2016 년에 scrypt 알고리즘은 IETF에 의해 RFC 7914로 공개되었습니다.

구체적으로는 알고리즘은 다른 암호 기반 KDF(RSA Laboratories의 인기있는 PBKDF2 등)에 비해 많은 양의 메모리를 사용하도록 설계되어 있으며, 하드웨어 구현의 크기와 비용이 훨씬 높아지기 때문에 공격자가 사용할 수있는 병렬 처리의 양이 제한됩니다.

따라서 PBKDF2보다 안전하다고 평가되며 미래에 BCrypt에 비해 더 경쟁력있다고 여겨진다.



Key Derivation Functions
단방향 해시함수의 개념이나 문제점은 이미 다룬 주제라서, 그외의 내용만 추가로 알아보았다.

키 유도 함수는 솔팅과 키 스트레칭으로 단방향 해시함수의 취약점을 보완한다.
다이제스트를 생성할 때 솔팅과 키 스트레칭을 반복하며 솔트와 패스워드 외에도 입력 값을 추가하여 공격자가 쉽게 다이제스트를 유추할 수 없도록 하고 보안의 강도를 선택할 수 있다.
이 함수들은 GPU와 같은 장비를 이용한 병렬화를 어렵게 하는 기능을 제공한다. 이와 같은 기능은 프로그램이 언어에서 제공하는 라이브러리만으로는 구현하기 어렵다.
대표적인 Key Derivation Function
PBKDF2
scrypt
bcrypt
argon2 (떠오르는 KDF)
PBKDF2
Password-Based Key Derivation Function
해시 함수의 컨테이너인 PBKDF2는 솔트를 적용한 후 해시 함수의 반복 횟수를 임의로 선택할 수 있다.
PBKDF2는 아주 가볍고 구현하기 쉬우며, SHA와 같이 검증된 해시 함수만을 사용한다.
PBKDF2의 기본 파라미터는 다음과 같은 5개 파라미터다.

DIGEST = PBKDF2(PRF, Password, Salt, c, DLen)  
PRF: 난수(예: HMAC)
Password: 패스워드
Salt: 암호학 솔트
c: 원하는 iteration 반복 수
DLen: 원하는 다이제스트 길이
Scrypt
2009년에 발표된 키 파생 기능 (KDF) 함수이다.
오프라인 brute force 공격에 대해 더 강력하지만, 많은 메모리와 CPU를 사용한다.

다이제스트 생성시 메모리 오버헤드를 갖도록 설계
하드웨어 구현을 하는데 크기와 비용이 훨씬 더 비싸기 때문에, 주어진 자원에서 공격자가 사용할 수 있는 병렬처리의 양이 한정적이다.

OpenSSL 1.1 이상을 제공하는 시스템에서만 작동한다.

여러 언어의 라이브러리로 제공된다.

scrypt의 파라미터는 다음과 같은 6개 파라미터다.

DIGEST = scrypt(Password, Salt, N, r, p, DLen)  
Password: 패스워드
Salt: 암호학 솔트
N: CPU 비용
r: 메모리 비용
p: 병렬화(parallelization)
DLen: 원하는 다이제스트 길이
Bcrypt
1999년에 발표된 blowfish 암호 기반의 암호화 해시 함수이다. - 패스워드 저장을 목적으로 설계
gensalt()의 "work factor"를 조정하는 것만으로 간단하게 시스템의 보안성을 높일 수 있다.
Blowfish 암호를 기반으로 설계된 암호화 함수이며 현재까지 사용중인 가장 강력한 해시 메커니즘 중 하나이다.

보안에 집착하기로 유명한 OpenBSD에서 사용하고 있다. (컴퓨터 보안에 특화된 오픈 소스 운영 체제)

Salting과 Key stretching으로 Rainbow table attack, brute-force 공격에 대비할 수 있다.

C, Java를 포함한 많은 플랫폼, 언어에서 사용할 수 있다.

다만 PBKDF2나 scrypt와는 달리 bcrypt는 입력 값으로 72 bytes character를 사용해야 하는 제약이 있다.

blowfish 암호란?
키(key) 방식의 대칭형 블록 암호
일반 목적으로 사용가능한 비특허 알고리즘
처리 시간과 자원 소비가 최적화되어 각종 온라인 게임의 패킷 암호화, 패킷 복호화에 주로 사용된다.
빠른 속도 덕분에 스마트폰 플랫폼에도 적합하다. 키가 자주 변경되지 않을때 최적의 성능을 보여준다.
스마트폰 메모리에서 서브키를 훔치면 취약할 수 있다.
무엇을 사용할까?
서드파티의 라이브러리에 의존하지 않으면서 사용자 패스워드의 다이제스트를 생성하려면 PBKDF2을 사용하면 된다.
구현하려는 시스템이 매우 민감한 정보를 다루고, 보안 시스템을 구현하는 데 많은 비용을 투자할 수 있다면 scrypt를 사용하면 된다.
매우 강력한 패스워드 다이제스트를 생성하는 시스템을 쉽게 구현하고 싶다면 bcrypt를 사용하는 것이 좋다.
argon2
2015년 패스워드 해싱 대회에서 우승작으로 선정된 키 유도 함수이다.
최근 비밀번호 크래킹 기술은 GPU와 ASIC과 같은 기술을 도입하여 높은 컴퓨팅 파워를 가지고 병렬적으로 접근한다.

기술의 발전으로 언젠가는 취약점이 발견된다 -> 새로운 알고리즘의 필요성
사용하는 목적에 맞게 파라미터 변경으로 적용이 가능하며 3 가지 종류가 있다.

암호를 해싱하는데 걸리는 시간이나 소요되는 메모리 양을 설정할 수 있게 설계되었다.

Argon2d : 더 빠르고 data-depending 메모리 액세스를 사용

Argon2i : 속도는 느리지만 data-independent 메모리 액세스를 사용

Argon2id : 혼용한 방식으로 password hashing 과 password 기반 키 유도에 적합

# 참고
패스워드 암호화 - PBKDF2, scrypt, bcrypt, argon2
https://velog.io/@palza4dev/%ED%8C%A8%EC%8A%A4%EC%9B%8C%EB%93%9C-%EC%95%94%ED%98%B8%ED%99%94-PBKDF2-scrypt-bcrypt-argon2

비밀번호 단방향 암호화에 대하여
https://pakss328.medium.com/%EB%B9%84%EB%B0%80%EB%B2%88%ED%98%B8-%EB%8B%A8%EB%B0%A9%ED%96%A5-%EC%95%94%ED%98%B8%ED%99%94%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC-f2739a1485e

왜 SHA-3을 사용하지 않는가
http://www.itworld.co.kr/news/108321

Password Hashing: Scrypt, Bcrypt and ARGON2
https://medium.com/analytics-vidhya/password-hashing-pbkdf2-scrypt-bcrypt-and-argon2-e25aaf41598e

패스워드 크래킹
http://wiki.hash.kr/index.php/%ED%8C%A8%EC%8A%A4%EC%9B%8C%EB%93%9C_%ED%81%AC%EB%9E%98%ED%82%B9