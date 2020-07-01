
    //----Auto update App------
    implementation 'com.google.android.play:core:1.7.3'
    ===================================================



import android.app.Activity;
import android.content.Context;
import android.content.Intent;
import android.content.IntentSender;
import android.os.Bundle;
import android.os.Handler;
import android.widget.RelativeLayout;

import com.google.android.play.core.appupdate.AppUpdateInfo;
import com.google.android.play.core.appupdate.AppUpdateManager;
import com.google.android.play.core.appupdate.AppUpdateManagerFactory;
import com.google.android.play.core.install.model.AppUpdateType;
import com.google.android.play.core.install.model.UpdateAvailability;
import com.google.android.play.core.tasks.OnFailureListener;
import com.google.android.play.core.tasks.OnSuccessListener;
import com.nextinnovation.pt.barcodescanner.R;


public class SplashActivity extends Activity {


    RelativeLayout rel_main;
    SplashActivity activity;
    AppUpdateManager appUpdateManager;
    Context context;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_splash);
        rel_main = findViewById(R.id.rel_main);

        this.activity = this;
        this.context = this;
        this.appUpdateManager = AppUpdateManagerFactory.create(this);
        UpdateApp();

//        new Handler().postDelayed(new Runnable() {
//            public void run() {
//                startMainActivity();
//                finish();
//            }
//        }, 2000);


    }


    public void onResume() {
        super.onResume();
        this.activity = this;
        this.appUpdateManager.getAppUpdateInfo().addOnSuccessListener(new OnSuccessListener() {
            public final void onSuccess(Object obj) {
                SplashActivity.this.onResume0SplashScreen((AppUpdateInfo) obj);
            }
        });
    }

    public void onResume0SplashScreen(AppUpdateInfo appUpdateInfo) {
        if (appUpdateInfo.updateAvailability() == UpdateAvailability.DEVELOPER_TRIGGERED_UPDATE_IN_PROGRESS) {
            try {
                this.appUpdateManager.startUpdateFlowForResult(appUpdateInfo, AppUpdateType.IMMEDIATE, this.activity, 101);
            } catch (IntentSender.SendIntentException e) {
                e.printStackTrace();
            }
        }
    }

    public void HomeScreen() {
        new Handler().postDelayed(new Runnable() {
            public void run() {
                SplashActivity.this.startActivity(new Intent(SplashActivity.this, HomeActivity.class));
            }
        }, 1000);
    }

    public void UpdateApp() {
        try {
            this.appUpdateManager.getAppUpdateInfo().addOnSuccessListener(new OnSuccessListener() {
                public final void onSuccess(Object obj) {
                    UpdateApp1SplashScreen((AppUpdateInfo) obj);
                }
            }).addOnFailureListener(new OnFailureListener() {
                public final void onFailure(Exception exc) {
                    UpdateApp2SplashScreen(exc);
                }
            });
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void UpdateApp1SplashScreen(AppUpdateInfo appUpdateInfo) {
        if (appUpdateInfo.updateAvailability() != UpdateAvailability.UPDATE_AVAILABLE || !appUpdateInfo.isUpdateTypeAllowed(AppUpdateType.IMMEDIATE)) {
            HomeScreen();
            return;
        }
        try {
            this.appUpdateManager.startUpdateFlowForResult(appUpdateInfo, AppUpdateType.IMMEDIATE, this.activity, 101);
        } catch (IntentSender.SendIntentException e) {
            e.printStackTrace();
        }
    }

    public void UpdateApp2SplashScreen(Exception exc) {
        exc.printStackTrace();
        HomeScreen();
    }

    public void onActivityResult(int i, int i2, Intent intent) {
        super.onActivityResult(i, i2, intent);
        if (i != 101) {
            return;
        }
        if (i2 != -1) {
            HomeScreen();
        } else {
            HomeScreen();
        }
    }

}
