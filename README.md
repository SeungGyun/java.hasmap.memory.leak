# HashMap 사용시 메모리 Leak 발생할 수있는 구조와 해결 방법

    public class HashMapLeakSample {
       	private Map<String,Temp> m = new HashMap<String,Temp>();
		public void set(String s, Temp t) {
			m.put(s, t);
		}
		
		public User getTemp(String s) {
		    return m.get(s);
		}
		
		public void removeUser(Socket s) {
		    m.remove(s);
		}
    }

위와 같이 Map을 관리하였을때 메모리 증가 현상이 발생 소지가 있음

약한참조 ( weak reference) + WeakHashMap 을 통하여 해결 

	public class HashMapClearSample {
       	private Map<Key,Temp> m = new WeakHashMap<Key,Temp>();
		public void set(String s, Temp t) {
			m.put(s, t);
		}
		
		public User getTemp(String s) {
		    return m.get(s);
		}
		
		public void removeUser(Socket s) {
		    m.remove(s);
		}
    }

여기서 Key << 이 부분을 객채화 하여 처리 --> hashCode, equals 구현해준다.

	@Override
	public int hashCode() {
		final int prime = 31;
		int result = 1;
		result = prime * result + ((mapKey == null) ? 0 : mapKey.hashCode());
		return result;
	}
	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		MapCacheKey other = (MapCacheKey) obj;
		if (mapKey == null) {
			if (other.getKey() != null)
				return false;
		} else if (!mapKey.equals(other.getKey()))
			return false;
		return true;
	}





참조 내용 

	Weak Reference 
	
	
	
	Java에서는 세 가지 주요 유형의 참조(Reference) 방식이 존재한다.   
	
	
	
	1. 강한 참조 (Strong Reference)
	
	– Integer prime = 1;   와 같은 가장 일반적인 참조 유형이다. prime 변수 는 값이 1 인 Integer 객체에 대한 강한 참조 를가진다.  이 객체를 가리키는 강한 참조가 있는 객체는 GC대상이 되지않는다.
	
	
	
	2. 부드러운 참조 (Soft Reference)
	
	– SoftReference<Integer> soft = new SoftReference<Integer>(prime); 와 같이 SoftReference Class를 이용하여 생성이 가능하다.  만약 prime == null 상태가 되어 더이상 원본(최초 생성 시점에 이용 대상이 되었던 Strong Reference) 은 없고 대상을 참조하는 객체가 SoftReference만 존재할 경우 GC대상으로 들어가도록 JVM은 동작한다. 다만 WeakReference 와의 차이점은 메모리가 부족하지 않으면 굳이 GC하지 않는 점이다.  때문에 조금은 엄격하지 않은 Cache Library들에서 널리 사용되는 것으로 알려져있다.
	
	
	
	3. 약한 참조 (Weak Reference)
	
	– WeakReference<Integer> soft = new WeakReference<Integer>(prime);   와 같이 WeakReference Class를 이용하여 생성이 가능하다.  prime == null 되면 (해당 객체를 가리키는 참조가 WeakReference 뿐일 경우) GC 대상이 된다.  앞서 이야기 한 내용과 같이 SoftReference와 차이점은 메모리가 부족하지 않더라도 GC 대상이 된다는 것이다.    다음 GC가 발생하는 시점에 무조건 없어진다.
	

관련 래퍼런스 참조 

https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=aim4u&logNo=221588713695

https://yunjigo92.github.io/2020/05/20/java_memory_leak.html ( 자바 메모리 릭 발생 할 수 있는 개발 스타일 정리 함)

