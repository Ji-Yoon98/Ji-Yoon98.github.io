---
title:  "Spring Boot Security"
excerpt: "✨Security 로그인 구현✨"

categories:
  - Spring boot
tags:
  - [Study, Spring boot]

permalink: /boot8/

toc: true

date: 2023-12-14
last_modified_at: 2023-12-14
---

## 🌿 Security 로그인

시큐리티로 로그인 기능을 하기 위해서는 필요한 bean을 추가해주는 config 클래스 파일을 추가하여 기본적인 세팅을 해야한다. 👇🏻

[시큐리티 설정 바로가기](https://ji-yoon98.github.io/boot7/)

### 회원 도메인 생성

```java

@Getter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@Entity
public class CustBas implements UserDetails {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private long id;
	
	@NotNull
	@Column(unique = true)
	private String custId;			// 고객아이디
	
	@NotNull
	private String custNm;			// 고객명
	
	private String custPassword;		// 고객패스워드
	
	@NotNull
	@Column(unique = true)
	private String custIdfyNo;		// 고객식별번호(주민번호)
	
	private String custBirth;		// 생년월일(yyyy.mm.dd)
	
	@NotNull
	private String custNo;			// 고객번호(전화번호)
	
	private String custSex;			// 고객성별
	
	private String custAddress;		// 고객주소
	
	@NotNull
	@Column(unique = true)
	private String custEmail;		// 고객이메일
	
	private String intmPurStusYn;		// 기기구매여부
	
	// DTO <-> Entity
	@Builder
	public CustBas( String custId, String custNm, String custPassword, String custIdfyNo, String custNo, String custEmail, String custBirth, String custAddress, String custSex, String intmPurStusYn) {
		this.custId = custId;
		this.custNm = custNm;
		this.custPassword = custPassword;
		this.custIdfyNo = custIdfyNo;
		this.custNo = custNo;
		this.custEmail = custEmail;
		this.custBirth = custBirth;
		this.custAddress = custAddress;
		this.custSex = custSex;
		this.intmPurStusYn = intmPurStusYn;
	}

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		Collection<GrantedAuthority> collection = new ArrayList<>();

        // 모든 회원은 user 권한을 가짐
        collection.add(new SimpleGrantedAuthority("ROLE_USER"));

        // Id가 admin인 회원은 admin권한도 가짐
        if (isAdmin()) {
        	collection.add(new SimpleGrantedAuthority("ROLE_ADMIN"));
        }

        return collection;
    }
	
	// 관리자 권한
	public boolean isAdmin() {
    return "admin".equals(custId);
  }

	
	// 사용자 id 반환
	@Override
	public String getUsername() {
		return getCustId();
	}
	
	// 사용자 비밀번호 반환
	@Override
	public String getPassword() {
		return getCustPassword();
	}
	
	// 계정 만료 여부, true: 만료X
	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	
	// 계정 잠금 여부, true: 잠금X
	@Override
	public boolean isAccountNonLocked() {
		return true;
	}

	// 패스워드 만료 여부, true: 만료X
	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}

	// 계정 사용 가능 여부, true: 사용가능
	@Override
	public boolean isEnabled() {
		return true;
	}

}

```

Spring Security에서 사용자 정보를 처리하기 위한 인터페이스인 UserDetails를 구현하여 해당 클래스(CustBas)는 Spring Security와의 통합을 지원하며, 사용자 계정과 관련된 정보를 제공.

- getAuthorities(): 사용자가 가진 권한들을 반환
- getUsername(): 사용자의 이름(일반적으로는 ID)을 반환
- getPassword(): 사용자의 암호화된 비밀번호를 반환
- isAccountNonExpired(): 계정이 만료되었는지 여부를 나타내는 boolean 값을 반환
- isAccountNonLocked(): 계정이 잠겨 있는지 여부를 나타내는 boolean 값을 반환
- isCredentialsNonExpired(): 사용자의 자격 증명(비밀번호 등)이 만료되었는지 여부를 나타내는 boolean 값을 반환
- isEnabled(): 계정이 활성화되었는지 여부를 나타내는 boolean 값을 반환

### Repository 생성

```java
public interface CustRepository extends JpaRepository<CustBas, Long> {
  Optional<CustBas> findByCustId(String custId);
}
```

### Service 생성

```java
@Service
@RequiredArgsConstructor
@Transactional(readOnly = true)
public class CustomUserDetailsService implements UserDetailsService {
	@Autowired
	CustRepository custRepository;
	
	@Override
	public UserDetails loadUserByUsername(String custId) throws UsernameNotFoundException {
		CustBas userData = custRepository.findByCustId(custId)
				.orElseThrow(() -> new UsernameNotFoundException("custId(%s) not found".formatted(custId)));
		
		return new User(userData.getCustId(), userData.getPassword(), userData.getAuthorities());
		
	}

}
```

- custId에 해당하는 사용자를 검색
- 사용자가 존재하지 않는 경우 예외 처리: orElseThrow 메서드를 사용하여 데이터가 없을 경우 UsernameNotFoundException을 발생
- UserDetails 객체 반환: 사용자 데이터가 존재하는 경우, 이 정보를 기반으로 User 객체를 생성하여 반환
