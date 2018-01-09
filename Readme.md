# ADS04 Android

## 수업 내용
- Permission 학습

## Code Review

### Manifest

```Java
<manifest xmlns:android="~~~~~"
    package="orgs.androidtown.permission">

    <!-- 권한설정 -->
    <!-- 실행시간 권한 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <!-- 설치 권한 -->
    <uses-permission android:name="android.permission.INTERNET" />

    // <application
    //     android:allowBackup="true"
    //     android:icon="@mipmap/ic_launcher"
    //     android:label="@string/app_name"
    //     android:roundIcon="@mipmap/ic_launcher_round"
    //     android:supportsRtl="true"
    //     android:theme="@style/AppTheme">
    //     <activity android:name=".MainActivity">
    //         <intent-filter>
    //             <action android:name="android.intent.action.MAIN" />

    //             <category android:name="android.intent.category.LAUNCHER" />
    //         </intent-filter>
    //     </activity>
    //     <activity android:name=".BaseActivity"></activity>
    // </application>

</manifest>
```
- <uses-permission: ~~~> 이 부분이 권한 설정 부분

### MainActivity

```Java
/**
 * 안드로이드 권한요청
 * 가. 일반적인 권한 요청 -> Manifest 에 설정
 *   - 디스크 쓰기,읽기
 *
 *
 *
 */

public class MainActivity extends BaseActivity {
    @Override
    public void init() {
        setContentView(R.layout.activity_main);
    }
}
```

### BaseActivity

```Java
public abstract class BaseActivity extends AppCompatActivity {

    private static final int REQ_CODE = 999;
    private static final String permissions[] = {
            Manifest.permission.READ_EXTERNAL_STORAGE,
            Manifest.permission.WRITE_EXTERNAL_STORAGE
    };

    public abstract void init();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        // 앱 버전 체크 - 호환성 처리
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            checkPermission();
        } else {
            init();
        }
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void checkPermission() {
        // 1. 권한이 있는지 여부 확인
        if (checkSelfPermission(Manifest.permission.READ_EXTERNAL_STORAGE)
                == PackageManager.PERMISSION_GRANTED &&
                checkSelfPermission(Manifest.permission.WRITE_EXTERNAL_STORAGE)
                        == PackageManager.PERMISSION_GRANTED) {
            // 진행 허용 처리
            init();
            // 2. 권한이 없으면 권한을 요청
        } else {
            // 2.1 요청할 권한을 정의
            String permissions[] = {
                    Manifest.permission.READ_EXTERNAL_STORAGE,
                    Manifest.permission.WRITE_EXTERNAL_STORAGE
            };
            // 2.2 권한 요청
            requestPermissions(permissions, REQ_CODE);
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        // 3. 권한 승인 여부 체크
        switch (requestCode) {
            case REQ_CODE:
                if (grantResults[0] == PackageManager.PERMISSION_GRANTED
                        && grantResults[1] == PackageManager.PERMISSION_GRANTED) {
                    // 진행 허용 처리
                    init();
                }
                break;
        }
    }
}
```

## 보충설명

### Permission?

> 승인, 권한이라는 의미이며, 안드로이드 OS에서 어플리케이션이 권한을 획득해야 사용자의 디바이스에 설치된 기능을 수행 할 수 있음

> 플랫폼은 악의적인 앱으로 부터 유저를 보호하는 장치가 필요함. 그렇지 않으면 무분별하게 네트워크 패킷을 사용하거나 사용자 몰래 녹음을 하는 등의 심각한 문제가 발생할 수 있음. 이를 위해서 안드로이드 중요 리소스 마다 permission을 두고 이를 얻지 못한 앱의 접근하는 것을 막고 있음

### 사용법 

- Manifest.xml 파일에 <uses-permission> 추가

- APP Version Check

- 권한 유무 확인

권한이 승인되어 있으면 init() 과 같이 초기화 함수 실행 <-> 승인되지 않은 권한이 있다면 권한 승인 요청

- 권한 승인 요청

권한 승인을 허가하면 init() 실행 <-> 권한 승인을 거절하면 finish() 호출하여 APP 종료

### 참고

- [사용 권한 선언](https://developer.android.com/training/permissions/declaring.html?hl=ko)
- [런타임에 사용 권한 요청](https://developer.android.com/training/permissions/requesting.html?hl=ko)
- [런타임 권한 사용시 주의사항](https://developer.android.com/training/permissions/usage-notes.html?hl=ko)

## TODO

- util성 Class 들을 정리하는 방법 혹은 이슈 생각해보기
- 필요할 떄 참고로 하기


## Retrospect

- 앱을 만들면서 이런 설정부분은 신경 쓰지 않았는데, 이런식으로 코딩이 되는 구나라는 것을 깨달았음.

## Output
- 생략


