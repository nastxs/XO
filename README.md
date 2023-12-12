<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/Primary_color"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/logo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fontFamily="@font/montserrat_alternates_semibold_italic"
        android:text="@string/logo"
        android:textAlignment="center"
        android:layout_marginTop="50sp"
        android:textColor="@color/text_color"
        android:textSize="30sp" />

    <EditText
        android:id="@+id/user_field"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:layout_below="@id/logo"
        android:backgroundTint="#fff"
        android:layout_marginTop="100sp"
        android:layout_marginHorizontal="40sp"
        android:inputType="text"
        android:textColor="@color/text_color"
        android:textColorHint="@color/text_color_hint"
        android:hint="@string/hint_user_field" />

    <Button
        android:id="@+id/main_btn"
        android:fontFamily="@font/montserrat_alternates_semibold_italic"
        android:layout_below="@+id/user_field"
        android:layout_marginTop="20sp"
        android:layout_marginHorizontal="100sp"
        android:backgroundTint="@color/btn_color"
        android:text="@string/main_btn_text"

        android:layout_width="match_parent"
        android:layout_height="wrap_content"

        />

    <TextView
        android:id="@+id/result_info"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:fontFamily="@font/montserrat_alternates_semibold_italic"

        android:textAlignment="center"
        android:layout_below="@+id/main_btn"
        android:layout_marginTop="50sp"
        android:textColor="@color/btn_color"
        android:textSize="25sp" />

    <ImageView
        android:id="@+id/imageView"
        android:layout_width="200sp"
        android:layout_height="200sp"
        android:layout_below="@+id/result_info"


        android:layout_marginTop="113dp"
        app:srcCompat="@drawable/google_cloud_logo_icon_171058" />


</RelativeLayout>




MainActivity
package com.example.xo;

import androidx.appcompat.app.AppCompatActivity;

import android.annotation.SuppressLint;
import android.os.AsyncTask;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;
import android.widget.Toast;

import org.json.JSONException;
import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;

public class MainActivity extends AppCompatActivity {

    private EditText user_field;
    private Button main_btn;
    private TextView result_info;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        user_field=findViewById(R.id.user_field);
        main_btn=findViewById(R.id.main_btn);
        result_info=findViewById(R.id.result_info);

        main_btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if(user_field.getText().toString().trim().equals(""))
                    Toast.makeText(MainActivity.this, R.string.no_user_input, Toast.LENGTH_LONG).show();
                else {

                String city = user_field.getText().toString();
                String key ="cf24076acf1c71338f9abd8f7ac6d87a";
                String url = "https://api.openweathermap.org/data/2.5/weather?q="+ city +"&appid="+ key +"&units=metric&lang=ru";
                    new GetURLData().execute(url);


                }
            }
        });

    }

    private class GetURLData extends AsyncTask<String, String, String>{
       protected void onPreExecute(){
           super.onPreExecute();
           result_info.setText("Ожидайте...");
        }


        @Override
        protected String doInBackground(String... strings) {
            HttpURLConnection connection = null;
            BufferedReader reader = null;
            try {
                URL url = new URL(strings[0]);
                connection = (HttpURLConnection) url.openConnection();
                connection.connect();

                InputStream stream = connection.getInputStream();
                reader = new BufferedReader(new InputStreamReader(stream));
                StringBuffer buffer = new StringBuffer();
                String line ="";

                while((line = reader.readLine())!=null)
                    buffer.append(line).append("\n");
                return buffer.toString();


            } catch (MalformedURLException e) {
                throw new RuntimeException(e);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }finally {
                if(connection !=null)
                    connection.disconnect();
                try {
                if(reader != null)

                        reader.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }

            }
            @SuppressLint("SetTextI18n")
            @Override
        protected void onPostExecute(String result){
           super.onPostExecute(result);

                try {
                    JSONObject obj = new JSONObject(result);
                    result_info.setText("Температура: "+obj.getJSONObject("main").getDouble("temp")+"\n"+ "Ощущается как: "+obj.getJSONObject("main").getDouble("feels_like"));
                } catch (JSONException e) {
                    throw new RuntimeException(e);
                }



            }

        }
    }
