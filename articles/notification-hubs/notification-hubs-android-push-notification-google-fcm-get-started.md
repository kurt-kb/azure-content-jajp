<properties
	pageTitle="Azure Notification Hubs と Firebase Cloud Messaging を使用した Android へのプッシュ通知の送信 | Microsoft Azure"
	description="このチュートリアルでは、Azure Notification Hubs と Firebase Cloud Messaging を使用して Android デバイスにプッシュ通知を送信する方法について学習します。"
	services="notification-hubs"
	documentationCenter="android"
	keywords="プッシュ通知,プッシュ通知,android プッシュ通知,fcm,firebase cloud messaging"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="07/05/2016"
	ms.author="wesmc"/>

# Azure Notification Hubs から Android へのプッシュ通知の送信

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##概要

> [AZURE.IMPORTANT] このトピックでは、Google Firebase Cloud Messaging (FCM) を使用したプッシュ通知について説明します。まだ Google Cloud Messaging (GCM) を使用している場合は、[Azure Notification Hubs と GCM を使用した Android へのプッシュ通知の送信](notification-hubs-android-push-notification-google-gcm-get-started.md)に関する記事を参照してください。

このチュートリアルでは、Azure Notification Hubs と Firebase Cloud Messaging を使用して Android アプリケーションにプッシュ通知を送信する方法について説明します。Firebase Cloud Messaging (FCM) を使用してプッシュ通知を受信する空の Android アプリケーションを作成します。



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

このチュートリアルの完成したコードについては、GitHub の[こちら](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase)からダウンロードできます。


##前提条件

> [AZURE.IMPORTANT] このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)を参照してください。

- このチュートリアルでは、上記のアクティブな Azure アカウント以外に、最新版の [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797) が必要です。
- Android 2.3 以上 (Firebase Cloud Messaging 用)。
- Firebase Cloud Messaging には Google Repository revision 27 以上が必要です。
- Google Play Services 9.0.2 以上 (Firebase Cloud Messaging 用)。
- このチュートリアルを完了することは、Android アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。


##新しい Android Studio プロジェクトを作成する

1. Android Studio で新しい Android Studio プロジェクトを開始する

   	![Android Studio - new project](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. **[電話とタブレット]** フォーム ファクターを選択し、サポートする **[最低限の SDK]** を選択します。その後、**[次へ]** をクリックします。

   	![Android Studio - project creation workflow](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. メイン アクティビティに **[Empty Activity (空のアクティビティ)]** を選択し、**[Next (次へ)]** をクリックして、**[Finish (完了)]** をクリックします。


##Firebase Cloud Messaging をサポートするプロジェクトを作成する

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##新しい Notification Hub を構成する

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6.通知ハブの **[設定]** ブレードで、**[Notification Services]**、[**Google (GCM)]** の順に選択します。先ほど [Firebase Console](https://firebase.google.com/console/) からコピーした FCM キーを入力し、**[保存]** をクリックします。

&emsp;&emsp;![Azure Notification Hubs - Google (GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

これで、通知ハブが Firebase Cloud Messaging と連動するように構成されました。接続文字列を使用して、プッシュ通知の受信と送信の両方にアプリを登録できます。

##<a id="connecting-app"></a>通知ハブにアプリを接続する

###プロジェクトへの Google Play Services の追加

[AZURE.INCLUDE [Play サービスの追加](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Azure Notification Hubs ライブラリを追加する


1. **[app (アプリ)]** の `Build.Gradle` ファイルの **dependencies** セクションに次の行を追加します。

		compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
		compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. **[dependencies]** セクションの後に次のリポジトリを追加します。

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### AndroidManifest.xml ファイルを更新する


1. FCM をサポートするには、[Google の FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId) を利用し、[登録トークンの取得](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register)に使用されるインスタンス ID リスナー サービスをコードに実装する必要があります。このチュートリアルでは、クラスに `MyInstanceIDService` という名前を付けます。
 
	次のサービス定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. FirebaseInstanceId API から FCM 登録トークンを受け取ったら、それを使用して [Azure 通知ハブに登録](notification-hubs-push-notification-registration-management.md)します。この登録はバックグラウンドで `RegistrationIntentService` という名前の `IntentService` を利用してサポートします。このサービスは FCM 登録トークンの更新も行います。
 
	次のサービス定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. 通知を受信するレシーバーも定義します。次のレシーバー定義を AndroidManifest.xml ファイルの `<application>` タグ内に追加します。`<your package>` プレースホルダーを、`AndroidManifest.xml` ファイルの先頭にある実際のパッケージ名に置き換えます。

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. 次の必要な FCM 関連のアクセス許可を `</application>` タグの下に追加します。`<your package>` は、`AndroidManifest.xml` ファイルの先頭にあるパッケージ名に必ず置き換えてください。

	これらのアクセス許可の詳細については、[Android 用 GCM Client アプリの設定](https://developers.google.com/cloud-messaging/android/client#manifest)に関するページと[Android 用 GCM Client アプリの Firebase Cloud Messaging への移行](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm)に関するページを参照してください。

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### コードを追加する


1. [プロジェクト] ビューで、**[アプリ]**、**[src]**、** [main]**、**[java]** と展開します。**[java]** のパッケージ フォルダーを右クリックして、**[新規]**、**[Java Class (Java クラス)]** と順にクリックします。`NotificationSettings` という名前の新しいクラスを追加します。

	![Android Studio - new Java class](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

	以下の `NotificationSettings` クラスのコード内にある次の 3 つのプレースホルダーを忘れずに更新してください。
	* **SenderId**: [Firebase Console](https://firebase.google.com/console/) でプロジェクト設定の **[Cloud Messaging (Cloud Messaging)]** タブで先ほど取得した送信者 ID。
	* **HubListenConnectionString**: ハブの **DefaultListenAccessSignature** 接続文字列。接続文字列をコピーするには、[Azure ポータル]で、ハブの **[設定]** ブレードにある **[アクセス ポリシー]** をクリックします。
	* **HubName**: [Azure ポータル]のハブ ブレードに表示される通知ハブの名前を使用します。

	`NotificationSettings` のコードは次のとおりです。

		public class NotificationSettings {
		    public static String SenderId = "<Your project number>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Your default listen connection string>";
		}

2. 上記の手順で `MyInstanceIDService` という名前の別の新しいクラスを追加します。これはインスタンス ID リスナー サービスの実装になります。

	このクラスのコードでは `IntentService` を呼び出し、バックグラウンドで [FCM トークンを更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)します。

		import android.content.Intent;
		import android.util.Log;
		import com.google.firebase.iid.FirebaseInstanceIdService;
		
		
		public class MyInstanceIDService extends FirebaseInstanceIdService {
		
		    private static final String TAG = "MyInstanceIDService";
		
		    @Override
		    public void onTokenRefresh() {
		
		        Log.d(TAG, "Refreshing GCM Registration Token");
		
		        Intent intent = new Intent(this, RegistrationIntentService.class);
		        startService(intent);
		    }
		};


3. `RegistrationIntentService` という名前の別の新しいクラスをプロジェクトに追加します。これが [FCM トークンの更新](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens)と[通知ハブへの登録](notification-hubs-push-notification-registration-management.md)を処理する `IntentService` の実装になります。

	次のコードをこのクラスに使用します。

		import android.app.IntentService;
		import android.content.Intent;
		import android.content.SharedPreferences;
		import android.preference.PreferenceManager;
		import android.util.Log;		
		import com.google.firebase.iid.FirebaseInstanceId;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class RegistrationIntentService extends IntentService {
		
		    private static final String TAG = "RegIntentService";
		
		    private NotificationHub hub;
		
		    public RegistrationIntentService() {
		        super(TAG);
		    }
		
		    @Override
		    protected void onHandleIntent(Intent intent) {
		
		        SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
		        String resultString = null;
		        String regID = null;
		        String storedToken = null;
		
		        try {
		            String FCM_token = FirebaseInstanceId.getInstance().getToken();
		            Log.d(TAG, "FCM Registration Token: " + FCM_token);
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            // Check if the token may have been compromised and needs refreshing.
		            else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            else {
		                resultString = "Previously Registered Successfully - RegId : " + regID;
		            }
		        } catch (Exception e) {
		            Log.e(TAG, resultString="Failed to complete registration", e);
		            // If an exception happens while fetching the new token or updating our registration data
		            // on a third-party server, this ensures that we'll attempt the update at a later time.
		        }
		
		        // Notify UI that registration has completed.
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(resultString);
		        }
		    }
		}


		
4. `MainActivity` クラスで、クラス宣言の上に次の `import` ステートメントを追加します。

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.content.Intent;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. クラスの最上位に、次のプライベート メンバーを追加します。これらを利用し、[Google の推奨事項に従って Google Play サービスが利用可能かどうかを確認](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)します。

	    public static MainActivity mainActivity;
    	public static Boolean isVisible = false;	
	    private static final String TAG = "MainActivity";
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. `MainActivity` クラスに、Google Play Services が利用可能かどうかを確認する次のメソッドを追加します。

	    /**
	     * Check the device to make sure it has the Google Play Services APK. If
	     * it doesn't, display a dialog that allows users to download the APK from
	     * the Google Play Store or enable it in the device's system settings.
	     */
	    private boolean checkPlayServices() {
	        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
	        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
	        if (resultCode != ConnectionResult.SUCCESS) {
	            if (apiAvailability.isUserResolvableError(resultCode)) {
	                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
	                        .show();
	            } else {
	                Log.i(TAG, "This device is not supported by Google Play Services.");
	                ToastNotify("This device is not supported by Google Play Services.");
	                finish();
	            }
	            return false;
	        }
	        return true;
	    }


7. `MainActivity` クラスに次のコードを追加します。このコードは、Google Play Services を確認してから、`IntentService` を呼び出して FCM 登録トークンを取得し、通知ハブに登録します。

	    public void registerWithNotificationHubs()
	    {
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with FCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. `MainActivity` クラスの `OnCreate` メソッドで、アクティビティの作成時に登録プロセスを開始するために次のコードを追加します。

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. 次の追加メソッドを `MainActivity` に追加して、アプリの状態を確認してアプリ内で報告します。

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }
	
	    public void ToastNotify(final String notificationMessage) {
	        runOnUiThread(new Runnable() {
	            @Override
	            public void run() {
	                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                TextView helloText = (TextView) findViewById(R.id.text_hello);
	                helloText.setText(notificationMessage);
	            }
	        });
	    }


10. `ToastNotify` メソッドでは *"Hello World"* `TextView` コントロールを使用し、アプリで状態と通知を継続的に報告します。activity\_main.xml レイアウトでは、そのコントロールに次の ID を追加します。

        android:id="@+id/text_hello"

11. 次に、AndroidManifest.xml で定義したレシーバーのサブクラスを追加します。`MyHandler` という名前の別の新しいクラスをプロジェクトに追加します。

12. 次の import ステートメントを `MyHandler.java` の先頭に追加します。

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.media.RingtoneManager;
		import android.net.Uri;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. `MyHandler` クラス用に次のコードを追加して、`com.microsoft.windowsazure.notifications.NotificationsHandler` のサブクラスにします。

	このコードによって `OnReceive` メソッドがオーバーライドされるため、ハンドラーは受信した通知を報告します。ハンドラーは `sendNotification()` メソッドを使用して Android の通知マネージャーにもプッシュ通知を送信します。`sendNotification()` メソッドは、アプリが動作していないときに通知を受信した場合に実行される必要があります。

		public class MyHandler extends NotificationsHandler {
		    public static final int NOTIFICATION_ID = 1;
		    private NotificationManager mNotificationManager;
		    NotificationCompat.Builder builder;
		    Context ctx;
		
		    @Override
		    public void onReceive(Context context, Bundle bundle) {
		        ctx = context;
		        String nhMessage = bundle.getString("message");
		        sendNotification(nhMessage);
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(nhMessage);
		        }
		    }
		
		    private void sendNotification(String msg) {
		
		        Intent intent = new Intent(ctx, MainActivity.class);
		        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
		
		        mNotificationManager = (NotificationManager)
		                ctx.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		                intent, PendingIntent.FLAG_ONE_SHOT);
		
		        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
		        NotificationCompat.Builder mBuilder =
		                new NotificationCompat.Builder(ctx)
		                        .setSmallIcon(R.mipmap.ic_launcher)
		                        .setContentTitle("Notification Hub Demo")
		                        .setStyle(new NotificationCompat.BigTextStyle()
		                                .bigText(msg))
		                        .setSound(defaultSoundUri)
		                        .setContentText(msg);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		}


14. Android Studio のメニュー バーで、**[Build (ビルド)]**、**[Rebuild Project (プロジェクトのリビルド)]** の順にクリックし、コードにエラーがないことを確認します。
15. お使いのデバイスでアプリを実行し、そのアプリが通知ハブに正常に登録されることを確認します。

	> [AZURE.NOTE] インスタンス ID サービスの `onTokenRefresh()` メソッドが呼び出されるまでは、初期起動時に登録が失敗する場合があります。更新すると、通知ハブに正常に登録されるようになります。

##プッシュ通知を送信する

[Azure ポータル]から通知を送信して、アプリでプッシュ通知の受信テストを行うことができます。以下に示す、ハブ ブレードの **[トラブルシューティング]** セクションを参照してください。

![Azure Notification Hubs - Test Send](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (省略可能) アプリから直接プッシュ通知を送信する

通常は、バックエンド サーバーを使用して通知を送信します。場合によっては、クライアント アプリケーションから直接プッシュ通知を送信できることが必要になります。このセクションでは、[Azure Notification Hubs REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx) を使用してクライアントから通知を送信する方法を説明します。

1. Android Studio の [プロジェクト] ビューで **[アプリ]**、**[src]**、**[main]**、**[res]**、**[layout]** の順に展開します。`activity_main.xml` レイアウト ファイルを開き、**[Text (テキスト)]** タブをクリックしてファイルのテキストの内容を更新します。次のコードで更新します。これにより、通知ハブにプッシュ通知メッセージを送信するための新しい `Button` コントロールと `EditText` コントロールが追加されます。このコードは一番下の `</RelativeLayout>` のすぐ前に追加します。

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Android Studio の [Project] ビューで **[App]**、**[src]**、**[main]**、**[res]**、**[values]** の順に展開します。`strings.xml` ファイルを開き、新しい `Button` コントロールと `EditText` コントロールで参照される文字列の値を追加します。これらはファイルの一番下の `</resources>` のすぐ前に追加します。

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. `NotificationSetting.java` ファイルで、次の設定を `NotificationSettings` クラスに追加します。

	`HubFullAccess` をハブの **DefaultFullSharedAccessSignature** 接続文字列で更新します。この接続文字列は [Azure ポータル]からコピーできます。これには、通知ハブの **[設定]** ブレードで **[アクセス ポリシー]** をクリックします。

		public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. `MainActivity.java` ファイルで、`MainActivity` クラスの上に次の `import` ステートメントを追加します。

		import java.io.BufferedOutputStream;
		import java.io.BufferedReader;
		import java.io.InputStreamReader;
		import java.io.OutputStream;
		import java.net.HttpURLConnection;
		import java.net.URL;
		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

6. `MainActivity.java` ファイルで、`MainActivity` クラスの先頭に次のメンバーを追加します。

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;

6. 通知ハブにメッセージを送信するには、POST 要求を認証するためのソフトウェア アクセス署名 (SaS) トークンを作成する必要があります。これを実行するには、接続文字列からキーのデータを解析し、「[共通概念](http://msdn.microsoft.com/library/azure/dn495627.aspx)」の REST API リファレンスで説明したように SaS トークンを作成します。次のコードでは、実装例を示します。

	`MainActivity.java` で、接続文字列を解析する次のメソッドを `MainActivity` クラスに追加します。

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
	     * to parse the connection string so a SaS authentication token can be
	     * constructed.
	     *
	     * @param connectionString This must be the DefaultFullSharedAccess connection
	     *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }


7. `MainActivity.java` で、SAS 認証トークンを作成する次のメソッドを `MainActivity` クラスに追加します。

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
	     * construct a SaS token from the access key to authenticate a request.
	     *
	     * @param uri The unencoded resource URI string for this operation. The resource
	     *            URI is the full URI of the Service Bus resource to which access is
	     *            claimed. For example,
	     *            "http://<namespace>.servicebus.windows.net/<hubName>"
	     */
	    private String generateSasToken(String uri) {
	
	        String targetUri;
	        String token = null;
	        try {
	            targetUri = URLEncoder
	                    .encode(uri.toString().toLowerCase(), "UTF-8")
	                    .toLowerCase();
	
	            long expiresOnDate = System.currentTimeMillis();
	            int expiresInMins = 60; // 1 hour
	            expiresOnDate += expiresInMins * 60 * 1000;
	            long expires = expiresOnDate / 1000;
	            String toSign = targetUri + "\n" + expires;
	
	            // Get an hmac_sha1 key from the raw key bytes
	            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
	            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
	
	            // Get an hmac_sha1 Mac instance and initialize with the signing key
	            Mac mac = Mac.getInstance("HmacSHA256");
	            mac.init(signingKey);
	
	            // Compute the hmac on input data bytes
	            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
	
	            // Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
	            String signature = URLEncoder.encode(
	                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
	
	            // Construct authorization string
	            token = "SharedAccessSignature sr=" + targetUri + "&sig="
	                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
	        } catch (Exception e) {
	            if (isVisible) {
	                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
	            }
	        }
	
	        return token;
	    }



8. `MainActivity.java` で、次のメソッドを `MainActivity` クラスに追加します。このメソッドは **[Send Notification]** ボタンのクリックを処理し、組み込みの REST API を使用してハブにプッシュ通知メッセージを送信します。

	    /**
	     * Send Notification button click handler. This method parses the
	     * DefaultFullSharedAccess connection string and generates a SaS token. The
	     * token is added to the Authorization header on the POST request to the
	     * notification hub. The text in the editTextNotificationMessage control
	     * is added as the JSON body for the request to add a GCM message to the hub.
	     *
	     * @param v
	     */
	    public void sendNotificationButtonOnClick(View v) {
	        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
	        final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
	
	        new Thread()
	        {
	            public void run()
	            {
	                try
	                {
	                    // Based on reference documentation...
	                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
	                    ParseConnectionString(NotificationSettings.HubFullAccess);
	                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
	                            "/messages/?api-version=2015-01");
	
	                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	
	                    try {
	                        // POST request
	                        urlConnection.setDoOutput(true);
	
	                        // Authenticate the POST request with the SaS token
	                        urlConnection.setRequestProperty("Authorization", 
								generateSasToken(url.toString()));
	
	                        // Notification format should be GCM
	                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
	
	                        // Include any tags
	                        // Example below targets 3 specific tags
	                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
	                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
							//		"tag1 || tag2 || tag3");
	
	                        // Send notification message
	                        urlConnection.setFixedLengthStreamingMode(json.length());
	                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
	                        bodyStream.write(json.getBytes());
	                        bodyStream.close();
	
	                        // Get reponse
	                        urlConnection.connect();
	                        int responseCode = urlConnection.getResponseCode();
	                        if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
	                        }
	                    } finally {
	                        urlConnection.disconnect();
	                    }
	                }
	                catch(Exception e)
	                {
	                    if (isVisible) {
	                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
	                    }
	                }
	            }
	        }.start();
	    }



##アプリケーションのテスト

####エミュレーターでのプッシュ通知

エミュレーターの内部でプッシュ通知をテストする場合は、エミュレーター イメージがアプリケーション用に選択した Google API レベルをサポートしていることを確認してください。イメージがネイティブの Google API をサポートしていない場合は、最終的に **SERVICE\_NOT\_AVAILABLE** 例外を受け取ることになります。

上記に加えて、**[設定]**、**[アカウント]** で、実行しているエミュレーターに Google アカウントを追加していることを確認してください。それ以外の場合、GCM での登録の試行が **AUTHENTICATION\_FAILED** 例外になる可能性があります。

####アプリケーションの実行

1. アプリケーションを実行し、登録 ID の登録完了が報告されていることを確認します。

   	![Testing on Android - Channel registration](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. ハブに登録されているすべての Android デバイスに送信される通知メッセージを入力します。

   	![Testing on Android - sending a message](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. **[Send Notification (通知の送信)]** を押します。アプリケーションが実行されているすべてのデバイスで、プッシュ通知メッセージを示す `AlertDialog` インスタンスが表示されます。実行中のアプリケーションがなくても事前にプッシュ通知に登録されているデバイスでは、Android 通知マネージャーで通知が受信されます。左上隅から下へスワイプすると、通知を表示できます。

   	![Testing on Android - notifications](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##次のステップ

次のステップとして「[Notification Hubs を使用したユーザーへのプッシュ通知]」チュートリアルをお勧めします。このチュートリアルでは、特定のユーザーに対してタグを使用して ASP.NET バックエンドから通知を送信する方法について説明しています。

対象グループごとにユーザーを区分する場合は、「[Notification Hubs を使用したニュース速報の送信]」のチュートリアルを参照してください。

Notification Hubs の全般的な情報については、「[Notification Hubs の概要]」を参照してください。

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs を使用したユーザーへのプッシュ通知]: notification-hubs-aspnet-backend-android-notify-users.md
[Notification Hubs を使用したニュース速報の送信]: notification-hubs-aspnet-backend-android-breaking-news.md
[Azure ポータル]: https://portal.azure.com

<!---HONumber=AcomDC_0706_2016-->