---
title: 안드로이드 카카오로 로그인 하기
author: breadkey
categories: study
tags: android
layout: post
---

# 1. KakaoSDK 설치(Gradle)
## 1.1 Repostiory 등록
build.gradle(Module: app)
```
repositories {
    ...
    mavenCentral()
    maven { url 'http://devrepo.kakao.com:8088/nexus/content/groups/public/' }
}
```
## 1.2 version 설정
gradle.properites
```
KAKAO_SDK_VERSION = 1.17.0 # 현재 최신 버전
```

## 1.3 로그인 SDK 의존성 등록
```
dependencies {
	...
	implementation group: 'com.kakao.sdk', name: 'usermgmt', version: project.KAKAO_SDK_VERSION
}
```

# 2. [앱생성 및 키해시 등록](https://developers.kakao.com/docs/android/getting-started#%EC%95%B1-%EC%83%9D%EC%84%B1)
# 3. [사용자 관리 설정](https://developers.kakao.com/docs/android/user-management#시작하기-전에)
# 4. SDK와 Application 연결
## 4.1 GlobalApplication 클래스 생성
```
public class GlobalApplication extends Application {
    private static volatile GlobalApplication instance = null;

    private static class KakaoSdkAdaptor extends KakaoAdapter {
        @Override
        public ISessionConfig getSessionConfig() {
            return new ISessionConfig() {
                @Override
                public AuthType[] getAuthTypes() {
                    return new AuthType[]{ AuthType.KAKAO_LOGIN_ALL };
                }

                @Override
                public boolean isUsingWebviewTimer() {
                    return false;
                }

                @Override
                public boolean isSecureMode() {
                    return false;
                }

                @Override
                public ApprovalType getApprovalType() {
                    return ApprovalType.INDIVIDUAL;
                }

                @Override
                public boolean isSaveFormData() {
                    return true;
                }
            };
        }

        @Override
        public IApplicationConfig getApplicationConfig() {
            return new IApplicationConfig() {
                @Override
                public Context getApplicationContext() {
                    return GlobalApplication.getGlobalApplicationContext();
                }
            };
        }
    }

    @Override
    public void onCreate() {
        super.onCreate();
        instance = this;

        KakaoSDK.init(new KakaoSdkAdaptor());
    }

    public static GlobalApplication getGlobalApplicationContext() {
        if(instance == null)
            throw new IllegalStateException("this application does not inherit com.kakao.GlobalApplication");
        return instance;
    }
}
```
이후 해당 클래스를 Manifest의 application으로 설정한다.

* **KakaoAdaptor**
: KakaoSdk와 애플리케이션을 연결해준다.
* **IApplicationConfig**
: Application이 가지고 있는 정보를 얻기위한 interface
* **ISessionConfig**
: Session을 생성하기 위해 필요한 옵션을 얻기위한 abstract class. 기본 설정이 이미 존재하므로 변경이 필요한 경우 상속해서 사용한다.

# 5. LoginAcitivity에 com.kakao.usermgmt.LoginButton 추가
# 6. ISessionCallback 구현 및 Session에 추가
```
private SessionCallback sessionCallback;

@Override
protected void onCreate(Bundle savedInstanceState) {
	...
	sessionCallback = new SessionCallback();
}

@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        if (Session.getCurrentSession().handleActivityResult(requestCode, resultCode, data)) {
            return;
        }

        super.onActivityResult(requestCode, resultCode, data);
    }

@Override
protected ovid onDestroy() {
	super.onDestroy();
	Session.getCurrentSession().removeCallback(sessionCallback);
}

private class SessionCallback implements ISessionCallback {
        @Override
        public void onSessionOpened() {
            redirectSignUpActivity();
        }

        @Override
        public void onSessionOpenFailed(KakaoException exception) {
            if (exception != null) {
                Logger.e(exception);
            }
        }
    }
```

# 7. 사용자 정보 Request
```
public void requestAuth() {
	ArrayList<String> keys = new ArrayList<>();
	keys.add("properties.nickname");

	UserManagement.getInstance().me(keys, new MeV2ReponseCallback() {
		@Override
		public void onSessionClosed(ErrorResult errorReusult) {
			// 세션이 종료됨
		}

		@Override
		public void onSuccess(MeV2Reponse Reulst) {
			id = result.getId();
			nickname = result.getNickname();
		}
	});
}
```
[사용자 정보 키값](https://developers.kakao.com/docs/android/user-management#사용자-정보-요청)