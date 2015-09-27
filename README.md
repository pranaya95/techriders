
# techriders

#Login page
package com.techriders.cabpool;


import android.app.Activity;
import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;

public class Login extends Activity implements OnClickListener {

	Button b1,b2;
	EditText e1,e2;
	/* (non-Javadoc)
	 * @see android.app.Activity#onCreate(android.os.Bundle)
	 */
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.login);
		e1=(EditText)findViewById(R.id.uname);
		e2=(EditText)findViewById(R.id.pass);
		b1=(Button)findViewById(R.id.signin);
		b2=(Button)findViewById(R.id.signup);
		b1.setOnClickListener(this);
		b2.setOnClickListener(this);
		
	}
	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		if(v==b1)
		{
			String uname=e1.getText().toString();
			String pass=e2.getText().toString();
			String url="http://10.0.2.2:9090/OlaHackathon/Login?uname="+uname+"&pass="+pass;
			Http H=new Http(url,this,null);
			H.httpcall();
		}else
		{
			Intent i = new Intent(this,Register.class);
			startActivity(i);
		}
		
	}
	
	

}


#SIGN UP ACTIVITY

package com.techriders.cabpool;

import android.app.Activity;
import android.content.Context;
import android.location.LocationManager;
import android.os.Bundle;
import android.telephony.TelephonyManager;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;

public class Register extends Activity implements OnClickListener {

	EditText name1,uname1,pass1,mob1,gender1,eid1;
	Button B;
	TelephonyManager T;
	String m;
	/* (non-Javadoc)
	 * @see android.app.Activity#onCreate(android.os.Bundle)
	 */
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.signup);
		T = (TelephonyManager)getSystemService(Context.TELEPHONY_SERVICE);
		m = T.getSimSerialNumber();
		initialize();
		B.setOnClickListener(this);
	}
	private void initialize() {
		// TODO Auto-generated method stub
		name1=(EditText)findViewById(R.id.name);
		uname1=(EditText)findViewById(R.id.uname);
		mob1=(EditText)findViewById(R.id.mob);
		pass1=(EditText)findViewById(R.id.password);
		name1=(EditText)findViewById(R.id.name);
		eid1=(EditText)findViewById(R.id.eid);
		B=(Button)findViewById(R.id.signup);
		
	}
	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		String name=name1.getText().toString();
		String uname=uname1.getText().toString();
		String mob=mob1.getText().toString();
		String gen=gender1.getText().toString();
		String eid=eid1.getText().toString();
		name=name.replace(' ', '*');
		eid=eid.replace('.','*');
	String pass=pass1.getText().toString();
	String url="http://10.0.2.2:9090/OlaHackathon/Insert?name="+name+"&eid="+eid+"&gender="+gen+"&mob="+mob+"&pass="+pass+"&uname="+uname+"&sno="+m;
	Http H=new Http(url,this,null);
	H.httpcall();
	}
	
	

}




#MainActivity of our app

package com.techriders.cabpool;



import android.os.Bundle;
import android.app.Activity;
import android.content.Intent;
import android.view.Menu;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;

public class MainActivity extends Activity implements OnClickListener {

	Button b1,b2;
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		b1=(Button)findViewById(R.id.share);
		b2=(Button)findViewById(R.id.goalone);
		b1.setOnClickListener(this);
		b2.setOnClickListener(this);
	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}

	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		if(v==b1)
		{
			Intent i=new Intent(this,sharing.class);
			startActivity(i);
		}else if(v==b2)
		{
			Intent i=new Intent(this,Alone.class);
			startActivity(i);
		}	
	}
	

}


#HTTP CLIENT CLASS

package com.techriders.cabpool;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.URI;

import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;

import android.content.Context;
import android.content.Intent;
import android.os.Handler;
import android.os.Message;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;

public class Http {
	private String url = new String();
	 String response = new String();
	 Context ctx;
	 ListView L;
	 MyHandler H = new MyHandler();
	 public Http(String url,Context ctx,ListView L)
	 {
		 this.url = url;
		 this.ctx = ctx;
		 this.L = L;
		 
	 }
	 
	 void httpcall()
	 {
		 HttpProcess P = new HttpProcess();
		 P.start();
	 }
	
	 	class HttpProcess extends Thread
	 	{
	 		public void run()
	 		{
	 			H.sendEmptyMessage(0);
	 		}
	 	}
	 	class MyHandler extends Handler
	 	{

			@Override
			public void handleMessage(Message msg) {
				// TODO Auto-generated method stub
				super.handleMessage(msg);
				try{
					HttpClient client=new DefaultHttpClient();
					HttpGet req=new HttpGet();
					 
					req.setURI(new URI(url));
					HttpResponse res=client.execute(req);
					//read response
					InputStreamReader in=new InputStreamReader(res.getEntity().getContent());
					BufferedReader br=new BufferedReader(in);
					String NL=System.getProperty("line.separator");
					StringBuffer s=new StringBuffer();
					String line="";
					while((line=br.readLine())!=null)
					{s.append(line);
					}	
				response=s.toString();
				if(response.equals("1")) 
				{
				//T.setText("Record Submitted.....");
					//Toast.makeText(ctx,"Registration done..please wait you will be automatically directed to your account", Toast.LENGTH_LONG).show();
				Intent i=new Intent(ctx,MainActivity.class);
				ctx.startActivity(i);
				}
				else if(response.equals("2"))
				{
					Toast.makeText(ctx,"Login failed.....", Toast.LENGTH_LONG).show();
				}else if(response.equals("3"))
				{
					Toast.makeText(ctx,"Registration done....", Toast.LENGTH_LONG).show();
					Intent i=new Intent(ctx,MainActivity.class);
					ctx.startActivity(i);	
					
				}
				else if(response.equals("4"))
				{
					
					Toast.makeText(ctx,"Registration failed....", Toast.LENGTH_LONG).show();
				}
			
	 		
	 	}catch(Exception e)
		{
		Toast.makeText(ctx, e.toString(), Toast.LENGTH_LONG).show();
			response=e.toString();	
		
		}
}
	 	}
}




#ALONE ACTIVITY

package com.techriders.cabpool;



import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.URI;
import java.util.List;
import java.util.Locale;

import org.apache.http.Header;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.impl.client.DefaultHttpClient;

import com.techriders.cabpool.Http.HttpProcess;
import com.techriders.cabpool.Http.MyHandler;

import android.app.Activity;
import android.content.Context;
import android.content.Intent;
import android.location.Address;
import android.location.Geocoder;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.CompoundButton;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.Toast;
import android.widget.CompoundButton.OnCheckedChangeListener;
import android.widget.EditText;
import android.widget.RadioButton;

public class Alone extends Activity implements OnCheckedChangeListener, OnClickListener, LocationListener {
	
	double Lat,Long;
	LocationManager LM;
	EditText source,destination;
	Button b;
	RadioButton r1,r2;
	String cat;
	TextView t;
	/* (non-Javadoc)
	 * @see android.app.Activity#onCreate(android.os.Bundle)
	 */
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.alone);
		LM = (LocationManager)getSystemService(Context.LOCATION_SERVICE);
		
		LM.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0, this);
		source=(EditText)findViewById(R.id.sour);
		destination=(EditText)findViewById(R.id.destinat);
		t=(TextView)findViewById(R.id.confirm);
		b=(Button)findViewById(R.id.ridealone);
		r1=(RadioButton)findViewById(R.id.mini);
		r2=(RadioButton)findViewById(R.id.sedan);
		r1.setOnCheckedChangeListener(this);
		r2.setOnCheckedChangeListener(this);
		b.setOnClickListener(this);
	}
	/* (non-Javadoc)
	 * @see android.widget.CompoundButton.OnCheckedChangeListener#onCheckedChanged(android.widget.CompoundButton, boolean)
	 */
	@Override
	public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
		// TODO Auto-generated method stub
		if(r1.isChecked())
		{
			 cat="mini";
		}
		else if(r2.isChecked())
		{
			cat="sedan";
		}
		
	}
	/* (non-Javadoc)
	 * @see android.view.View.OnClickListener#onClick(android.view.View)
	 */
	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		String url="http://sandbox-t.olacabs.com/v1/bookings/create?pickup_lat="+Lat+"&pickup_lng="+Long+"&pickup_mode=NOW&category="+cat;
		Http H=new Http(url,this);
		H.httpcall();
		
	}
	
	public class Http {
		private String url = new String();
		 String response = new String();
		 Context ctx;
		 MyHandler H = new MyHandler();
		 public Http(String url,Context ctx)
		 {
			 this.url = url;
			 this.ctx = ctx;
			 
		 }
		 
		 void httpcall()
		 {
			 HttpProcess P = new HttpProcess();
			 P.start();
		 }
		
		 	class HttpProcess extends Thread
		 	{
		 		public void run()
		 		{
		 			H.sendEmptyMessage(0);
		 		}
		 	}
		 	class MyHandler extends Handler
		 	{

				@Override
				public void handleMessage(Message msg) {
					// TODO Auto-generated method stub
					super.handleMessage(msg);
					
					try{
						HttpClient client=new DefaultHttpClient();
						HttpGet req=new HttpGet();
						 
						req.setURI(new URI(url));
						req.setHeader("X-APP-Token" , "6ab7b7ba00f5453a96b607d7514fb4bf");
						req.setHeader("Authorization" , "Bearer 4c9a4926556f4612b432d20f17dccb56");
						HttpResponse res=client.execute(req);
						//read response
						InputStreamReader in=new InputStreamReader(res.getEntity().getContent());
						BufferedReader br=new BufferedReader(in);
						String NL=System.getProperty("line.separator");
						StringBuffer s=new StringBuffer();
						String line="";
						while((line=br.readLine())!=null)
						{s.append(line);
						}	
					response=s.toString();
					Toast.makeText(ctx,response, Toast.LENGTH_LONG).show();
					response=response.replace("{", "");
					response=response.replace("}", "");
					response=response.replace(",", "  ");
					t.setText(response);
					
					
		 	}catch(Exception e)
			{
			Toast.makeText(ctx, e.toString(), Toast.LENGTH_LONG).show();
				response=e.toString();	
			
			}
					
		 	}
}
	}

	
	@Override
	public void onLocationChanged(android.location.Location L) {
		// TODO Auto-generated method stub
		Lat = L.getLatitude();
	    Long = L.getLongitude();
	}
	@Override
	public void onStatusChanged(String provider, int status, Bundle extras) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void onProviderEnabled(String provider) {
		// TODO Auto-generated method stub
		
	}
	@Override
	public void onProviderDisabled(String provider) {
		// TODO Auto-generated method stub
		
	}
}

	
	
	


#SHARING ACTIVITY

package com.techriders.cabpool;

import java.util.Calendar;
import java.util.List;
import java.util.Locale;

import android.app.Activity;
import android.content.Context;
import android.location.Address;
import android.location.Geocoder;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.os.Bundle;
import android.telephony.TelephonyManager;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.CompoundButton;
import android.widget.CompoundButton.OnCheckedChangeListener;
import android.widget.EditText;
import android.widget.RadioButton;
import android.widget.Toast;



public class sharing extends Activity implements OnClickListener, LocationListener, OnCheckedChangeListener {
	
	double Lat,Long;
	LocationManager LM;
	EditText source, destination,seats;
	String filter;
	Button b;
	RadioButton r1,r2;
	TelephonyManager T;
	String m;
	/* (non-Javadoc)
	 * @see android.app.Activity#onCreate(android.os.Bundle)
	 */
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.sharing);
		LM = (LocationManager)getSystemService(Context.LOCATION_SERVICE);
		LM.requestLocationUpdates(LocationManager.GPS_PROVIDER, 0, 0, this);
		T = (TelephonyManager)getSystemService(Context.TELEPHONY_SERVICE);
		m = T.getSimSerialNumber();
		source=(EditText)findViewById(R.id.source);
		destination=(EditText)findViewById(R.id.destination);
		seats=(EditText)findViewById(R.id.seats);
		b=(Button)findViewById(R.id.ssearch);
		r1=(RadioButton)findViewById(R.id.y);
		r2=(RadioButton)findViewById(R.id.n);
		b.setOnClickListener(this);
		r1.setOnCheckedChangeListener(this);
		r2.setOnCheckedChangeListener(this);
		
	}

	@Override
	public void onClick(View v) {
		// TODO Auto-generated method stub
		String s=source.getText().toString();
		String d=destination.getText().toString();
		Calendar C=Calendar.getInstance();
				String cd=C.get(Calendar.YEAR)+"-"+(C.get(Calendar.MONTH)+1)+"-"+C.get(Calendar.DATE);
				String ct=C.get(Calendar.HOUR)+"-"+(C.get(Calendar.MINUTE)+1)+"-"+C.get(Calendar.SECOND);
				String time=cd+"*"+ct;
				time=time.replace('/', 'a');
				time=time.replace(':', 'b');
				s=s.replace(" ", "*");
				d=d.replace(" ", "*");
				
		String url="http://10.0.2.2:9090/OlaHackathon/Sharing?s="+s+"&d="+d+"&lat="+Lat+"&Long="+Long+"&seats="+seats+"&filter="+filter+"&time="+time+"&sno="+m;
		Http H=new Http(url,this,null);
		H.httpcall();
	}

	@Override
	public void onLocationChanged(Location L) {
		// TODO Auto-generated method stub
		Lat = L.getLatitude();
	    Long = L.getLongitude();
		getAddress(Lat,Long);
	}

	private void getAddress(double lat, double l) {
		// TODO Auto-generated method stub
		try{
			Geocoder GC = new Geocoder (this,Locale.getDefault());
			List<Address> address = GC.getFromLocation(lat, l, 1);
			StringBuffer SB = new StringBuffer();
			if(address.size()>0)
			{
				Address add = address.get(0);
				for(int i=0;i<add.getMaxAddressLineIndex();i++)
				{
					SB.append(add.getAddressLine(i)+"");
				}
				Toast.makeText(getApplicationContext(), lat+","+l+","+SB, Toast.LENGTH_LONG).show();
				String res=SB.toString();
			}
			
		}catch(Exception e)
    	{Toast.makeText(getApplicationContext(), e.getMessage(), Toast.LENGTH_LONG).show();
    	}
	}
	@Override
	public void onStatusChanged(String provider, int status, Bundle extras) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void onProviderEnabled(String provider) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void onProviderDisabled(String provider) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
		// TODO Auto-generated method stub
		if(r1.isChecked())
		{
			 filter="yes";
		}
		else if(r2.isChecked())
		{
			filter="no";
		}
		
	}
	

}


#PLIST ACTIVITY

package com.techriders.cabpool;

import android.app.Activity;
import android.os.Bundle;
import android.widget.ListView;

public class Plist extends Activity {
	
	ListView l;

	/* (non-Javadoc)
	 * @see android.app.Activity#onCreate(android.os.Bundle)
	 */
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		// TODO Auto-generated method stub
		super.onCreate(savedInstanceState);
		setContentView(R.layout.patnerlist);
		l=(ListView)findViewById(R.id.listView1);
		
	}
	

}



#XML FILES

#MAIN ACTIVITY XML FILE


<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/let"
        android:layout_width="221dp"
        android:layout_height="wrap_content"
        android:layout_x="52dp"
        android:layout_y="19dp"
        android:text="@string/let"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <Button
        android:id="@+id/share"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="89dp"
        android:layout_y="69dp"
        android:text="@string/share" />

    <TextView
        android:id="@+id/or"
        android:layout_width="86dp"
        android:layout_height="wrap_content"
        android:layout_x="124dp"
        android:layout_y="129dp"
        android:text="@string/or"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <Button
        android:id="@+id/goalone"
        android:layout_width="114dp"
        android:layout_height="wrap_content"
        android:layout_x="92dp"
        android:layout_y="164dp"
        android:text="@string/go_alone" />

</AbsoluteLayout>

#ALONE.XML

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/tv1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/source"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <EditText
        android:id="@+id/sour"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="text" />

    <TextView
        android:id="@+id/tv2"
        android:layout_width="wrap_content"
        android:layout_height="40dp"
        android:text="@string/destination"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <EditText
        android:id="@+id/destinat"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="text" />

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/category"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <RadioButton
        android:id="@+id/mini"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/mini" />

    <RadioButton
        android:id="@+id/sedan"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/sedan" />

    <Button
        android:id="@+id/ridealone"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/baride" />

    <TextView
        android:id="@+id/confirm"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:text="@string/con"
        android:textAppearance="?android:attr/textAppearanceLarge" />

</LinearLayout>




#LOGIN.XML


<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/username"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:inputType="text"
        android:id="@+id/uname"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10" >

        <requestFocus />
    </EditText>

    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/password"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:id="@+id/pass"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPassword" />

    <Button
        android:id="@+id/signin"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/signin" />

    <Button
        android:id="@+id/signup"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/signup" />

</LinearLayout>


#PATNERLIST.XML



<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/plist"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <ListView
        android:id="@+id/listView1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" >
    </ListView>

</LinearLayout>

#SHARE.XML

<AbsoluteLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <TextView
        android:id="@+id/tv1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="7dp"
        android:layout_y="16dp"
        android:text="@string/source"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <EditText
        android:id="@+id/source"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_x="7dp"
        android:layout_y="40dp"
        android:ems="10"
        android:inputType="text" />

    <TextView
        android:id="@+id/tv2"
        android:layout_width="wrap_content"
        android:layout_height="40dp"
        android:layout_x="6dp"
        android:layout_y="79dp"
        android:text="@string/destination"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <EditText
        android:id="@+id/destination"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_x="2dp"
        android:layout_y="110dp"
        android:ems="10"
        android:inputType="text" />

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="-2dp"
        android:layout_y="149dp"
        android:text="@string/total"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <Button
        android:id="@+id/ssearch"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_x="2dp"
        android:layout_y="376dp"
        android:text="@string/search" />

    <RadioButton
        android:id="@+id/n"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="4dp"
        android:layout_y="320dp"
        android:text="@string/n" />

    <RadioButton
        android:id="@+id/y"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="4dp"
        android:layout_y="274dp"
        android:text="@string/y" />

    <TextView
        android:id="@+id/tv3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_x="10dp"
        android:layout_y="236dp"
        android:text="@string/choice1"
        android:textAppearance="?android:attr/textAppearanceLarge" />

    <EditText
        android:id="@+id/seats"
        android:layout_width="66dp"
        android:layout_height="wrap_content"
        android:layout_x="7dp"
        android:layout_y="195dp"
        android:ems="10"
        android:inputType="number" />

</AbsoluteLayout>



#SIGNUP.XML

<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <TextView
        android:id="@+id/textView1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/name"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:id="@+id/name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPersonName" >

        <requestFocus />
    </EditText>

    <TextView
        android:id="@+id/textView5"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/password"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:id="@+id/password"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textPassword" />

    <TextView
        android:id="@+id/textView2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/username"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:inputType="text"
        android:id="@+id/uname"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10" />

    <TextView
        android:id="@+id/textView3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/phone"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:id="@+id/mob"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="phone" />

    <TextView
        android:id="@+id/textView4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/emailid"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:id="@+id/eid"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10"
        android:inputType="textEmailAddress" />

    <TextView
        android:id="@+id/textView6"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/gender"
        android:textAppearance="?android:attr/textAppearanceMedium" />

    <EditText
        android:inputType="text"
        android:id="@+id/gender"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:ems="10" />

    <Button
        android:id="@+id/button1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/register" />

</LinearLayout>







#ANDROID MANIFEST FILE


<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.techriders.cabpool"
    android:versionCode="1"
    android:versionName="1.0" >

    <uses-sdk
        android:minSdkVersion="8"
        />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        <activity
            android:name="com.techriders.cabpool.Login"
            android:label="@string/app_name" >
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity 
            android:name=".Alone"></activity>
         <activity 
            android:name=".MainActivity"></activity>
          <activity 
            android:name=".Plist"></activity>
           <activity 
            android:name=".sharing"></activity>
           <activity 
            android:name=".Register"></activity>
    </application>

</manifest>



#SERVELET FILES

#servelt for LOGIN


package com.ola;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class Login
 */
@WebServlet("/Login")
public class Login extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public Login() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		PrintWriter out=response.getWriter();
		try {			
			Class.forName("com.mysql.jdbc.Driver").newInstance();
			Connection cn=DriverManager.getConnection("jdbc:mysql://localhost:3309/olahack","root","shubham");
			//out.println("14");
			Statement smt=cn.createStatement();
		    String uname=request.getParameter("uname");
		   String pass=request.getParameter("pass");
		    ResultSet rs=smt.executeQuery("select * from registration where username='"+uname+"' and password='"+pass+"'");
			  
			     if(rs.next())
			    { 
			    	 out.print("1");
			    }
			     else
			    {
			    	out.print("2");
			    }
		} catch (Exception e) {
			// TODO Auto-generated catch block
			out.print(e);
			//out.print("3");
		}
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		
	}

}



#SHARING SERVLET FILE



package com.ola;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.text.ParseException;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class Sharing
 */
@WebServlet("/Sharing")
public class Sharing extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public Sharing() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		PrintWriter out=response.getWriter();
		ResultSet rs = null;
		try {
			
			//out.println("1");
			
			Class.forName("com.mysql.jdbc.Driver").newInstance();
			Connection cn=DriverManager.getConnection("jdbc:mysql://localhost:3309/olahack","root","shubham");
			//out.println("1");
			Statement smt=cn.createStatement();
		    String s=request.getParameter("source");
		    String d=request.getParameter("destination");
		    String time=request.getParameter("time");
		    String lat=request.getParameter("Lat");
		    String logi=request.getParameter("Logi");
		    String seats=request.getParameter("seats");
		    String f=request.getParameter("filter");
		    String sno=request.getParameter("sno");
		    time=time.replace('a', '/');
			time=time.replace('b', ':');
			time=time.replace('*', ' ');
			String[] temp=time.split(" ");
			s=s.replace("*", " ");
			d=d.replace(" ", " ");
			rs=smt.executeQuery("select name,gender from registration where simno='"+sno+"'");
			String a=null;
			String b=null;
			if(rs.next())
	    	{
	    		a=rs.getString(1);
	    		b=rs.getString(2);
		    String q="insert into sharing (name,lat,logi,totalseat,time,source,destination) values('"+a+"','"+lat+"','"+logi+"','"+seats+"','"+time+"','"+s+"','"+d+"')";
		     smt.executeUpdate(q);
			//out.print("3");
		     if(f.contentEquals("no"))
		     
		    	 rs=smt.executeQuery("select name from registration");
		    	 
		     else if(f.contentEquals("yes"))
		     rs=smt.executeQuery("select name from registration where gender='"+b+"'");
		    	 String c=" ",e1=" ",t4=" ",t5=" ",t6=" ";
		    	 Double t1=0.0,t2=0.0,t7=0.0,t8=0.0;
		    	 int t3=0;
		    	
		    	 
		    	 while(rs.next())
		    	 {
		    		c=c+rs.getString(1)+",";
		    	 }
		    	 String[] comb=c.split(",");
		    	 int l=comb.length;
		    	 Double td,dist;
		    	 //rs=smt.executeQuery("select * from sharing");
		    	for(int i=0;i<l;i++)
		    	{
		    	rs=smt.executeQuery("select * from sharing");
		    	 while(rs.next())
		    	 {
		    		if(comb[i].contentEquals(rs.getString(2)))
		    		{
		    			t1=Double.parseDouble(rs.getString(3));
		    			t7=Double.parseDouble(lat);
		    			t8=Double.parseDouble(logi);
		    			t2=Double.parseDouble(rs.getString(4));
		    			t3=Integer.parseInt(rs.getString(5));
		    			t4=rs.getString(6);
		    			t5=rs.getString(7);
		    			t6=rs.getString(8);
		    			String[] time1=t4.split(" ");
		    			td=(double) diff(time,t4);
		    			dist=distance(t7,t8,t1,t2);
		    			if((t6.contentEquals(d))&&(td<=10)&&(dist<=200))
		    			{
		    				e1=e1+rs.getString(2)+",";
		    			}
		    		 break;
		    		}
		    		 
		    	 }
		    	
		    	}
		    	 
		    	out.print(e1); 
		     }//if content is yes
	    	
	    	
	    	//main if
		} catch (Exception e) {
			// TODO Auto-generated catch block
			out.print(e);
			out.print("4");
		}
		out.flush();
		
	}

	long diff(String a,String b)
	{
		java.text.DateFormat df = new java.text.SimpleDateFormat("hh:mm:ss");
        java.util.Date date1 = null;
		try {
			date1 = df.parse("18:40:10");
			
		} catch (ParseException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		 java.util.Date date2 = null;
		try {
			date2 = df.parse("19:05:15");
		} catch (ParseException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
        long diff = date2.getTime() - date1.getTime();
        
        return diff;
        	
	}
	
	private double distance(double lat1, double lng1, double lat2, double lng2) {

	    double earthRadius = 3958.75; // in miles, change to 6371 for kilometer output

	    double dLat = Math.toRadians(lat2-lat1);
	    double dLng = Math.toRadians(lng2-lng1);

	    double sindLat = Math.sin(dLat / 2);
	    double sindLng = Math.sin(dLng / 2);

	    double a = Math.pow(sindLat, 2) + Math.pow(sindLng, 2)
	        * Math.cos(Math.toRadians(lat1)) * Math.cos(Math.toRadians(lat2));

	    double c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a));

	    double dist = earthRadius * c * 1609.344;

	    return dist; // output distance, in MILES
	}
	
}





#INSERT SERVLET FILE




import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class Insert
 */
@WebServlet("/Insert")
public class Insert extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public Insert() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		PrintWriter out=response.getWriter();
		try {
			
			//out.println("1");
			
			Class.forName("com.mysql.jdbc.Driver").newInstance();
			Connection cn=DriverManager.getConnection("jdbc:mysql://localhost:3309/olahack","root","shubham");
			//out.println("1");
			Statement smt=cn.createStatement();
		    String mob=request.getParameter("mob");
		    String name=request.getParameter("name");
		    name=name.replace('*', ' ');
		    String uname=request.getParameter("uname");
		    String pass=request.getParameter("pass");
		    String eid=request.getParameter("eid");
		    String sno=request.getParameter("sno");
		    eid=eid.replace('*','.');
		    String gender=request.getParameter("gender");
		    //String name="Shubham Barsaiya";
		    String q="insert into registration (name,username,mobile,gender,eid,password,sno) values('"+name+"','"+uname+"','"+mob+"','"+gender+"','"+eid+"','"+pass+"','"+sno+"')";
		     smt.executeUpdate(q);
			out.print("3");
		} catch (Exception e) {
			// TODO Auto-generated catch block
			out.print(e);
			out.print("4");
		}
		out.flush();
		
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
	}

}





