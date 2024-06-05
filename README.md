# NotificationDemo
NotificationDemo is an Android application that demonstrates how to create and display notifications using the Android notification framework.

# Table of Contents
Overview\
Features\
Prerequisites\
Installation\
Usage
# Overview
This application showcases how to implement notifications in an Android app. When a button is clicked, a notification with a specified title and content is generated and displayed.

# Features
Create and display notifications.\
Notification includes a title, content, and an icon.\
Tapping the notification launches the main activity of the app.

# Prerequisites
Android Studio
Android device or emulator running Android API level 21 or higher

# Usage
Launch the application on your Android device or emulator.\
Tap the button in the main activity.\
A notification will be displayed with the specified title and content.\
Tap the notification to return to the main activity of the app.

# Code Overview
## MainActivity.kt
The MainActivity class is the main entry point of the application. It initializes the button and sets up a click listener to generate the notification.


    package com.example.notificationdemo3

    import androidx.appcompat.app.AppCompatActivity
    import android.os.Bundle
    import android.widget.Button
    import android.widget.TextView

    class MainActivity : AppCompatActivity() {
        lateinit var button: Button

        override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)
            setContentView(R.layout.activity_main)

            button = findViewById(R.id.button)

            button.setOnClickListener {
                generateNotification()
        }
    }

    private fun generateNotification() {
        NotificationUtils.createNotification(this, "Title", "Content")
    }
}

## NotificationUtils.kt
The NotificationUtils object handles the creation and display of notifications. It sets up the notification channel, builds the notification, and displays it using the NotificationManagerCompat.

    package com.example.notificationdemo3

    import android.Manifest
    import android.app.NotificationChannel
    import android.app.NotificationManager
    import android.app.PendingIntent
    import android.content.Context
    import android.content.Intent
    import android.content.pm.PackageManager
    import android.os.Build
    import androidx.core.app.ActivityCompat
    import androidx.core.app.NotificationCompat
    import androidx.core.app.NotificationManagerCompat

    object NotificationUtils {

    private const val CHANNEL_ID = "my_channel_id"
    private const val NOTIFICATION_ID = 1

    fun createNotification(context: Context, title: String, content: String) {
        createNotificationChannel(context.applicationContext)

        val intent = Intent(context.applicationContext, MainActivity::class.java).apply {
            flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TASK
        }

        val pendingIntent = PendingIntent.getActivity(
            context.applicationContext,
            0,
            intent,
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) {
                PendingIntent.FLAG_MUTABLE
            } else {
                0
            }
        )

        val builder = NotificationCompat.Builder(context.applicationContext, CHANNEL_ID)
            .setSmallIcon(R.drawable.ic_notification)
            .setContentTitle(title)
            .setContentText(content)
            .setPriority(NotificationCompat.PRIORITY_DEFAULT)
            .setContentIntent(pendingIntent)
            .setAutoCancel(true)

        val notificationManager = NotificationManagerCompat.from(context.applicationContext)
        if (ActivityCompat.checkSelfPermission(context.applicationContext, Manifest.permission.POST_NOTIFICATIONS) != PackageManager.PERMISSION_GRANTED) {
            return
        }
        notificationManager.notify(NOTIFICATION_ID, builder.build())
    }

    private fun createNotificationChannel(context: Context) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(
                CHANNEL_ID,
                "My Channel",
                NotificationManager.IMPORTANCE_DEFAULT
            ).apply {
                description = "My Channel Description"
            }

            val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
            notificationManager.createNotificationChannel(channel)
         }
        }
    }

# Android Manifest File 

    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
    <uses-permission android:name="android.permission.INTERNET" />
