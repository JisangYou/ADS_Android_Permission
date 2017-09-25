# Permission
## permisiion이란?

- 승인, 권한이라는 의미이며, 안드로이드 OS에서 어플리케이션이 권한을 획득해야 사용자의 디바이스에 설치된 기능을 수행 할 수 있음

- 플랫폼은 악의적인 앱으로 부터 유저를 보호하는 장치가 필요함.
 그렇지 않으면 무분별하게 네트워크 패킷을 사용하거나 사용자 몰래 녹음을 하는 등의 심각한 문제가 발생할 수 있음. 이를 위해서 안드로이드 중요 리소스 마다 permission을 두고 이를 얻지 못한 앱의 접근하는 것을 막고 있음

## 사용법
- manifest에 권한을 설정

```Java
<manifest xmlns:android="~~~~~"
    package="orgs.androidtown.permission">

    <!-- 권한설정 -->
    <!-- 실행시간 권한 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <!-- 설치 권한 -->
    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name=".BaseActivity"></activity>
    </application>

</manifest>
```

- activity에서 사용법

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
