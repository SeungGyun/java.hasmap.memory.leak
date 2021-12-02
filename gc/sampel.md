제2나라 메모리 leak 현상 덤프 분석
자바 덤프 파일 가져오기  ( 버전에 맞는 jmap 지정한뒤 명령어 실행 필요)
./jmap -dump:file=./real_dump.log 4829

덤프 체크 하는법 
ha457.jar
java -Xmx12g -jar ha457.jar
화면실행되면 덤프 파일 가져온다.

leakSize  랭킹
735461728	735461728	0	735,461,728 bytes (30.91 %) of Java heap is used by 611,181 instances of byte[]	byte[]
280633568	280633568	760	280,633,568 bytes (11.8 %) of Java heap is used by 44 instances of java/util/Hashtable$Entry Contains 11 instances of the following leak suspects: - array of java/util/HashMap$Node holding 27,189,752 bytes at 0x7295e3930 - array of java/util/HashMap$Node holding 27,186,624 bytes at 0x7245cd308 - array of java/util/HashMap$Node holding 27,185,376 bytes at 0x719d1cd48 - array of java/util/HashMap$Node holding 10,147,456 bytes at 0x71e2961d8 - array of java/util/HashMap$Node holding 16,360,080 bytes at 0x7245f1138 - array of java/util/HashMap$Node holding 10,058,776 bytes at 0x7c3362f98 - array of java/util/HashMap$Node holding 16,360,080 bytes at 0x7db9f53e8 - array of java/util/HashMap$Node holding 10,089,896 bytes at 0x718a65660 - array of java/util/HashMap$Node holding 32,775,872 bytes at 0x729c59d58 - array of java/util/HashMap$Node holding 16,510,728 bytes at 0x71f78c910 - array of java/util/HashMap$Node holding 32,741,144 bytes at 0x7225a2bd8	array of java/util/Hashtable$Entry
156047736	156047736	0	156,047,736 bytes (6.56 %) of Java heap is used by 505,526 instances of int[]	int[]
138363160	138363160	8192	138,363,160 bytes (5.82 %) of Java heap is used by 446 instances of java/util/HashMap$Node	array of java/util/HashMap$Node
127620248	127620248	0	127,620,248 bytes (5.36 %) of Java heap is used by 266,011 instances of java/util/regex/Matcher	java/util/regex/Matcher
116077264	116077264	0	116,077,264 bytes (4.88 %) of Java heap is used by 460,917 instances of java/lang/StringBuilder	java/lang/StringBuilder
74384344	74384344	0	74,384,344 bytes (3.13 %) of Java heap is used by 161,576 instances of array of java/lang/Object	array of java/lang/Object
54958392	54958392	262144	54,958,392 bytes (2.31 %) of Java heap is used by 16,547 instances of java/util/HashMap$Node	array of java/util/HashMap$Node

Hash 관련 상세 로그 & byte 관련 상세 로그 
500,434,664 (21.03%) [32] 2 org/springframework/aop/framework/InterceptorAndDynamicMethodMatcher 0x72380a7d8
|- 498,822,496 (20.97%) [120] 6 org/springframework/aop/aspectj/AspectJAroundAdvice 0x70b115710
|- 498,821,216 (20.97%) [32] 2 org/springframework/aop/aspectj/annotation/LazySingletonAspectInstanceFactoryDecorator 0x70b12c070
|- 498,821,000 (20.97%) [32] 2 net/neo/core/cache/redis/aspect/CacheAspect 0x70bbf9240
| |- 360,440,768 (15.15%) [24] 1 net/neo/core/cache/map/MapCache 0x70bab1730
| | |- 360,440,744 (15.15%) [64] 2 java/util/Hashtable 0x70bab1740
| | |- 360,440,624 (15.15%) [88] 3 array of java/util/Hashtable$Entry 0x70bab1770
| | | |- 303,727,224 (12.77%) [40] 3 java/util/Hashtable$Entry 0x71dc30858
| | | | |- 290,224,808 (12.2%) [40] 3 java/util/Hashtable$Entry 0x71dc3f740
| | | | | |- 290,210,464 (12.2%) [40] 3 java/util/Hashtable$Entry 0x71dc402b8
| | | | | | |- 280,633,808 (11.8%) [40] 2 java/util/Hashtable$Entry 0x71dc686e0
| | | | | | | |- 280,633,688 (11.8%) [64] 2 java/util/Hashtable 0x71dc68760
| | | | | | | | |- 280,633,568 (11.8%) [760] 44 array of java/util/Hashtable$Entry 0x74f378470
| | | | | | | | |- 56 (0%) [32] 2 java/util/Collections$SynchronizedSet 0x7375d1298
| | | | | | | |- 80 (0%) [24] 1 java/lang/String 0x71dc68700
| | | | | | |- 9,576,536 (0.4%) [64] 2 java/util/Hashtable 0x71dc40338
| | | | | | |- 80 (0%) [24] 1 java/lang/String 0x71dc402d8
| | | | | |- 14,224 (0%) [64] 2 java/util/Hashtable 0x71dc3f7c0
| | | | | |- 80 (0%) [24] 1 java/lang/String 0x71dc3f760
| | | | |- 13,502,304 (0.57%) [64] 1 java/util/Hashtable 0x71dc308d0
| | | | |- 72 (0%) [24] 1 java/lang/String 0x71dc30878
| | | |- 54,959,128 (2.31%) [40] 2 java/util/Hashtable$Entry 0x71dc306a0
| | | |- 1,754,184 (0.07%) [40] 3 java/util/Hashtable$Entry 0x71a3627b0
| | |- 56 (0%) [32] 2 java/util/Collections$SynchronizedSet 0x71a362a68
| |- 138,380,200 (5.82%) [112] 7 net/neo/core/cache/redis/RedisService$$EnhancerBySpringCGLIB$$76514200 0x70bbf9258
|- 184 (0%) [40] 3 org/springframework/aop/aspectj/annotation/BeanFactoryAspectInstanceFactory 0x70b12c088




예상 되는 부분 
1.	파일 읽기고 해제 정상적으로 됬는지확인
2.	Map 관련  너무 많이 쌓이는 현상 MapCache 시스템 구조 정리 필요 
개선 내용
1.	hashtable static 하게 사용하는 이슈 
2.	WeakHashMap 테이블로 변경 
3.	키 값을 강결합이서 약결합으로 변경 

개선 내용 테스트 
cache size 7 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 518409376
cache size 13 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 517209184
cache size 19 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 516008992
cache size 27 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 514311904
cache size 32 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 513111712
cache size 35 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 511814688
cache size 38 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 510514480
cache size 43 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 508917408
cache size 45 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 507620384
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 506420192
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 505023152
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 503426080
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 502225888
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 500928864
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 499631840
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 498431648
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 496734560
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 495437536
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 494237344
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 492940320
cache size 47 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 491740128
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 490043040
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 488395952
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 487495808
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 485698704
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 484551712
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 483351520
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 482054496
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 480357408
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 479157216
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 477860192
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 476463152
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 474962912
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 473665888
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 472368864
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 470768608
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 469471584
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 468174560
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 466974368
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 465277280
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 463980256
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 462780064
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 461082976
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 459785952
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 458585760  ---- 순차 적으로 메모리가 감소함
cache size 5 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 515325656 — 중간 가비지 컬랙션 발생하여 메모리 증가 
cache size 12 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 514925592
cache size 18 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 513725400
cache size 24 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 512525208
cache size 32 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 510828120
cache size 35 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 509627928
cache size 38 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 508330904
cache size 43 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 506633816
cache size 45 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 505433624
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 504136600
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 502739560
cache size 50 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 501239320
cache size 49 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 499942296
cache size 48 heapSize : 536870912 heapMaxSize : 8579448832 heapFreeSize : 498742104
관련 래퍼런스 참조 
https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aim4u&logNo=221588713695
https://yunjigo92.github.io/2020/05/20/java_memory_leak.html ( 자바 메모리 릭 발생 할 수 있는 개발 스타일 정리 함)


