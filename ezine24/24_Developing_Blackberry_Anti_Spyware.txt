                                                
              .;               .-.              
  .-.  .-.    ;;-. .-..-.      `-' . ,';.  .-.  
.;.-' ;      ;;  ;;   ;' ;.   ;'   ;;  ;;.;.-'  
 `:::'`;;;;'.;`  ``;;'(   ;_.;:._.';  ;;  `:::' 
                       `-'        ;    `.       
ECHO MAGAZINE VOLUME IX, ISSUE XXIV, PHILE 0x03.TXT

Developing BlackBerry Anti Spyware Application - y3dips
y3dips/at/echo/dot/or/dot/id



-----[ Pendahuluan

Kurang lebih 2 tahun yang lalu, saya dan @chopstick_ sempat melakukan
kegiatan untuk men"develop" sebuah aplikasi security untuk mobile
device, yang dalam hal ini BlackBerry[1]. Aplikasi security ini kurang
lebih akan menjadi satu set tool security untuk blackberry, berisi
beberapa modul-modul security lainnya, lengkap dengan fitur UI-nya.

Mengapa kami pilih BlackBerry, karena saat itu Blackberry security
sedang menjadi topik yang hangat dan di perbincangkan di dunia keamanan
internasional, dimulai dengan Aplikasi spyware yang di bungkus update
oleh "Etisalat"[2], sampai kepada bermunculannya banyak sekali aplikasi
sejenis. Bahkan @chopstick_ merupakan salah seorang yang membuat
aplikasi sejenis[3] sekaligus membuatkan removal untuk spyware miliknya
dan aplikasi sejenis lainnya.

Proses Pengembangannya sendiri mungkin baru memakan waktu kurang lebih
1 minggu efektif, sampai akhirnya mulai tersendat-sendat dikarenakan 
beberapa hal non-teknis, sampai akhirnya kami sendiri lupa bahwa 
aplikasi ini pernah kami develop. 

Berikut adalah modul "SMS spyware detection" yang merupakan bagian
saya, yang waktu itu saya rencanakan untuk ditampilkan pada
idsecconf2011, tetapi karena satu dan lain hal akhirnya tidak jadi. 
Dan sebelum saya lupa dan kode-kode tersebut menjadi tidak berguna 
(dan juga karena akhir-akhir ini malah lebih sering berintraksi dengan
python) maka saya merilis sebagian kode untuk modul yang sudah saya
kembangkan, dengan harapan ada yang berminat dan ingin melanjutkannya.  

-----[ BlackBerry Spyware Application

Sangat banyak sekali aplikasi Blackberry spyware, baik yang berbayar
ataupun yang gratis, tetapi terdapat beberapa yang cukup terkenal, dan
harganya relatif mahal :)

1. FlexiSpy (flexyspy.com) dengan harga licensi pertahun US $149 -$349 :)
2. Mobile Spy (mobile-spy.com) dengan harga licensi 3 bulan US $50

atau yang tidak terkenal, tetapi di list pada Blackberry App World,
seperti:

3. Teen text Spy seharga US $7.50

Adapun fitur-fitur yang ditawarkan, pada versi yang paling lengkap
adalah:

- Baca SMS
- Baca Call logs
- Baca Email
- Mengetahui Lokasi GPS (location tracking)
- Email relay
- Notifikasi perubahan SIMCARD
- Remote Listening
- Mobile Call Tapping

-----[ Developing BlackBerry Anti Malware

Sebenarnya banyak juga aplikasi security untuk blackberry, baik dari
sejenis firewall, anti virus sampai anti malware itu sendiri, bahkan
chopstick_ sendiri selain merilis spyware, juga merilis anti
spyware[3]. Aplikasi removal spyware milik @chopstick_ salah satu cara
kerjanya adalah dengan memanfaatkan database signature dari seluruh
aplikasi spyware yang ada, sehingga apabila di deteksi, aplikasi
tersebut akan melaporkan dan apabila memungkinkan akan menghapus
aplikasi spyware yang terinstall dan bahkan diset tersembunyi tersebut. 

Tetapi tentu saja aplikasi ini sangat bergantung pada Database
Signature, sehingga selalu terlambat 1 langkah dari pembuat spyware.

Oleh karena itu kami berusaha membuat aplikasi yang akan mendeteksi
prilaku dari spyware/malware baru yang bahkan belum memiliki atau
sempat di"signature", dan user diharapkan nantinya akan dapat
berinteraksi dengan aplikasi, mendapatkan laporan rutin, mendeteksi
kejanggalan dari berbagai kegiatan ber-sms, email, telpon, video,
photo, dsb

-------[ How SMS Spyware Work

Seperti telah kita ketahui dari fitur-fitur yang umumnya ditawarkan
oleh aplikasi spyware, membaca pesan SMS merupakan salah satu fitur
yang di tawarkan dari tiap-tiap aplikasi tersebut.

Aplikasi spyware umumnya melakukan "penyadapan" terhadap setiap SMS
yang masuk dan kemudian mengirimkan/memforward setiap SMS yang diterima
ke nomor yang di tentukan oleh pemilik spyware.

Kurang lebih seperti ini bagan untuk membaca SMS yang terkirim

[SMS sent] ----->------ [ Recepient ]
                |
                |[Spyware agent]
                |
                |______ [ Spyware owner ] 

Spyware SMS agent akan melakukan listening dan men"triger" aplikasi
untuk mengirimkan sms ke pemilik, dan melakukan pembersihan, seperti
penghapusan SMS log (atau bahkan memaksa device untuk tidak me-log dari
awal)

-------[ How Our SMS Spyware Detection Works

Seperti yang telah di singgung diatas, bahwa salah satu modul untuk
aplikasi yang kami kembangkan adalah SMS Spyware detection (khususnya
yang saya kembangkan), yang diperuntukkan untuk mendeteksi apabila
terjadi aktifitas "spying" pada layanan text messaging/sms di
blackberry.

Modul deteksi SMS spyware ini memiliki cara kerja yang sederhana,
setiap SMS yang masuk akan di tandai, yang dalam hal ini setiap SMS
yang keluar akan di hash lalu kemudian disimpan ke dalam sebuah file
dengan format nama bbsexurity-smslog-"TANGGAL".txt

----------[ Bagan SMS Spyware Detection

       1. Send Normal SMS 
[SMS] ------ > [Hash[x]] -----> [File log] 

       2. Malware/Spyware send SMS
[SMS] -------> [Hash[y]] -----> [File log] <--- Check Procedure [Hash[x] == Hash[y]]
                                                                          |
                                                    [Store/Alert] <-------|

---------[ Penjelasan Aplikasi dalam Kode

Bagi anda yang sudah sering mengembangkan aplikasi untuk blackberry
atau java developer, mungkin sudah tidak bingung lagi untuk langsung
mengintegrasikan dan memahami kode-kode yang kami pergunakan.  Tetapi,
bagi yang ingin mencobanya, saya mengembangkannya menggunakan Eclipse
dengan Blackberry SDK.

Berikut adalah beberapa bagian penting yang perlu kita bahas:
1. Berinteraksi dengan SMS system milik BlackBerry

Untuk melakukan interaksi dengan SMS Messaging system milik Blackberry,
kita mempergunakan SMS API dari RIM yaitu
net.rim.blackberry.api.sms.SMS

------- SMSHook.java -----------
15         public void HookSMS()
16         {
17             smsoutListener = new SMSOUTListener();
18             SMS.addSendListener(smsoutListener);
19             hooked = true;
20         }
21         
------- SMSHook.java -----------

2. Melakukan Hash terhadap SMS yang dikirim

Hal ini dilakukan dengan modul ShaMethod.java, yang menggunakan SHA untuk
hasing.  Saat SMS dikirimkan, maka pada saat yang sama SMS tersebut akan di
Hash (dalam hal ini dengan SHA)

----- SMSOUTListener.java -------
 22         if (message instanceof TextMessage)
 23         {
 24             UiApplication.getUiApplication().invokeLater( new Runnable()
 25             {
 26                 public void run() 
 27                 {   
 28                     //sha the message
 29                     String raw = (String) ShaMethod.sha(msg);
----- SMSOUTListener.java -------

3. Apa yang dilakukan dengan Signature SMS

Hasil dari hash dalam hal ini akan di tuliskan perbaris dalam satu file
dengan format nama bbsexurity-smslog-"TANGGAL".txt

untuk membuat file tersebut unik dan sekaligus membatasi jumlah maksimal
SMS, maka ditambahkan tanggal, bulan dan Tahun

----- SMSOUTListener.java -------
 30                     
 31                     try //save to file
 32                     {             
 33                     String data= (raw+"\r\n");
 34                     
 35                     //name the file as a date
 36                     Date dt = new Date();
 37                     SimpleDateFormat sfd = new SimpleDateFormat("dd-MM-yy");
 38                     String filename = sfd.formatLocal(dt.getTime());
 39                     
 40                     FileConnection conn = (FileConnection)Connector.open("file:///store/home/user/bbsexurity-smslog-"+filename+".txt",Connector.READ_WRITE);                   
 41                     long x =0;
----- SMSOUTListener.java -------

yang hasilnya akan kurnag lebih sbb:

----- bbsexurity-smslog-07-11-11.txt -------------

80e2389e820031c04a4d162b2158aa4b27bd4e4f
80e2389e820031c04a4d162b2158aa4b27bd4e4f
80e2389e820031c04a4d162b2158aa4b27bd4e4f
11d51fad4a279e3ca4e06f4a4c02d64076b2b317
6f0eb460fc95f26f96af1c253111b1c876439cee
b4170fb861398271396d335a352e85bc1364d43e
6f0eb460fc95f26f96af1c253111b1c876439cee

----- bbsexurity-smslog-07-11-11.txt -------------

4. Bagamana proses pendeteksiannya

Dan akan langsung di periksa dan dibandingkan, apabila Hash SMS yang
baru dikirim ternyata telah ada di file log tersebut

----- SMSOUTListener.java -------
 49                                 //read file
 50                                 try {
 51                                      InputStream stream = conn.openInputStream();
 52                                      LineReader lineReader = new LineReader(stream);
 53                                      String line = new String(lineReader.readLine());
 54                                      while(!line.equals("EOF"))
 55                                      {
----- SMSOUTListener.java -------

5. Proses Alert/Result

Pada saat ditemukan 2 hash yang sama dari SMS untuk nomer yang berbeda maka
bisa dimunculkan Alert (//Helpers.alertMsg("SMS sent twice!");) atau di log ke
file lainnya (dalam hal ini saya sengaja hanya melakukan log ke file lain untuk
keperluan selanjutnya)

----- SMSOUTListener.java -------
 57                                          if(line.equals(raw))
 58                                             {
 59                                              //Helpers.alertMsg("SMS sent twice!");
 60                                              SMSLogResult.logSMS("Double SMS Detected", num);
 61                                              break;
 62                                             }       
----- SMSOUTListener.java -------

Dan apabila ternyata Hash dari sms yang baru dikirim belum ada, maka
akan di tambahkan ke log file/database dalam hal ini file
bbsexurity-result-"TANGGAL".txt

----- SMSOUTListener.java -------
 73                             OutputStream out = conn.openOutputStream(x);
 74                             byte[] b = data.getBytes();
 75                        
 76                             if(conn.canWrite())         
 77                                 {                                             
 78                                 out.write(b);
 79                                 }
----- SMSOUTListener.java -------

Berikut adalah contoh file Log result:

------ bbsexurity-result-07-11-11.txt --------

Double SMS Detected, sms://31337
Double SMS Detected, sms://666
Double SMS Detected, sms://9191

------ bbsexurity-result-07-11-11.txt --------

Nah, akan ada 1 modul lagi untuk melakukan pemrosesan file log, dan
skripnya belum disempurnakan karena akan berhubungan dengan modul
lainnya, terkait model "centralize yang rencananya akan dibuat"

draftnya terdapat di modul FileProcessing.java

Catatan: Untuk Hash, menggunakan SHA1Digest dari library Crypto
(ShaMethod.java) Untuk proses hooking ke sms memanfaatkan API
"sms.SendListener", yang juga kemungkinan dipergunakan oleh aplikasi
spyware/malware.

-------[ Kesimpulan

Aplikasi ini masih jauh dari sempurna, apalagi modul SMS, tetapi
mudah-mudahan memberikan pemahaman yang bermanfaat buat semua, logika
ini berguna juga untuk deteksi email dsb, karena umumnya aplikasi
spyware menggunakan teknik yang sama.

Oh iya, ini aplikasi java pertama yang saya develop dan juga pertama 
kalinya saya koding dengan java (otodadak :D); ternyata merupakan
yang terakhir sampai saat ini (karena setelahnya saya belum punya 
kesempatan untuk menyentuh java lagi), jadi harap dimaklumi apabila 
kode-kode dari aplikasi terlihat amburadul, yang terpenting adalah
ide-ide yang ingin saya bagi mudah-mudahan bermanfaat.

------[ Kode

Kode lengkapnya bisa didownload di
http://code.google.com/p/bb-smspyware-detect/ beserta User
Interface-nya.

Beberapa potongan modul yang mungkin dapat dilihat dan dipelajari
langsung dibagian akhir artikel ini.

-----[ Reference

[1] http://blackberry.com
[2] http://www.theregister.co.uk/2009/07/14/blackberry_snooping/
[3] http://chirashi.zenconsult.net/2009/10/kiss-your-blackberry-spyware-goodbye/


-----[ Lampiran Kode]

------- SMSHook.java -----------
 1 package com.killmalware.bbsec;
 2 
 3 import net.rim.blackberry.api.sms.SMS;
 4     
 5     public class SMSHook 
 6     {
 7         private boolean hooked;
 8         private SMSOUTListener smsoutListener;
 9         
10         public SMSHook()
11         {
12             hooked = false;
13         }
14         
15         public void HookSMS()
16         {
17             smsoutListener = new SMSOUTListener();
18             SMS.addSendListener(smsoutListener);
19             hooked = true;
20         }
21         
22         public void unHookSMS()
23         {
24             SMS.removeSendListener(smsoutListener);
25             hooked = false;
26         }
27         
28         public boolean isHooked()
29         {
30             return hooked;
31         }
32     }
------- SMSHook.java -----------

------- ShaMethod.java -----------
  1 package com.killmalware.bbsec;
  2 
  3 import java.io.UnsupportedEncodingException;
  4 import net.rim.device.api.crypto.SHA1Digest;
  5 
  6 public class ShaMethod {
  7     public static String sha(final String msg)
  8     
  9     {
 10         SHA1Digest sha1Digest = new SHA1Digest();
 11         String sha = msg;
 12 
 13         byte[] inpData = null;
 14         try {
 15             inpData = sha.getBytes("UTF-8");
 16         } catch (UnsupportedEncodingException e) {
 17             e.printStackTrace();
 18         }
 19         sha1Digest.update(inpData, 0, inpData.length);
 20         byte[] digest = sha1Digest.getDigest();
 21         StringBuffer shaRes = new StringBuffer(40); //40 hex char is size of 160-bit SHA-1 result
 22         for (int a = 0; a < digest.length; a++)
 23         {
 24             String tmp = Integer.toHexString(0xff & digest[a]);
 25             if(tmp.length() == 1) //If hex value is "0X" then tmp is just one digit "X"
 26             {
 27                 shaRes.append('0');
 28             }
 29             shaRes.append(tmp);
 30         }
 31         return shaRes.toString();
 32     }
 33 }
------- ShaMethod.java -----------

------- SMSOUTListener.java ------
  1 package com.killmalware.bbsec;
  2 
  3 import java.io.IOException;
  4 import java.io.InputStream;
  5 import java.io.OutputStream;
  6 import java.util.Date;
  7 import javax.microedition.io.Connector;
  8 import javax.microedition.io.file.FileConnection;
  9 import javax.wireless.messaging.TextMessage;
 10 import net.rim.blackberry.api.sms.SendListener;
 11 import net.rim.device.api.i18n.SimpleDateFormat;
 12 import net.rim.device.api.io.LineReader;
 13 import net.rim.device.api.ui.UiApplication;
 14 
 15 public class SMSOUTListener implements SendListener
 16 {
 17 
 18     public boolean sendMessage(javax.wireless.messaging.Message message)
 19     {
 20         final String num = message.getAddress();
 21         final String msg = ((TextMessage) message).getPayloadText();
 22         if (message instanceof TextMessage)
 23         {
 24             UiApplication.getUiApplication().invokeLater( new Runnable()
 25             {
 26                 public void run() 
 27                 {   
 28                     //sha the message
 29                     String raw = (String) ShaMethod.sha(msg);
 30                     
 31                     try //save to file
 32                     {             
 33                     String data= (raw+"\r\n");
 34                     
 35                     //name the file as a date
 36                     Date dt = new Date();
 37                     SimpleDateFormat sfd = new SimpleDateFormat("dd-MM-yy");
 38                     String filename = sfd.formatLocal(dt.getTime());
 39                     
 40                     FileConnection conn = (FileConnection)Connector.open("file:///store/home/user/bbsexurity-smslog-"+filename+".txt",Connector.READ_WRITE);                   
 41                     long x =0;
 42                         if (!conn.exists())
 43                             {                              
 44                                 conn.create();        
 45                             } 
 46                         else
 47                             {
 48                                 x = conn.fileSize();
 49                                 //read file
 50                                 try {
 51                                      InputStream stream = conn.openInputStream();
 52                                      LineReader lineReader = new LineReader(stream);
 53                                      String line = new String(lineReader.readLine());
 54                                      while(!line.equals("EOF"))
 55                                      {
 56                                         //compare
 57                                          if(line.equals(raw))
 58                                             {
 59                                              //Helpers.alertMsg("SMS sent twice!");
 60                                              SMSLogResult.logSMS("Double SMS Detected", num);
 61                                              break;
 62                                             }       
 63                                          else 
 64                                             { 
 65                                              line = new String(lineReader.readLine());
 66                                             }
 67                                      }
 68                                      stream.close();
 69                                      stream = null;
 70                                      } 
 71                                 catch(IOException e) {}
 72                             }
 73                             OutputStream out = conn.openOutputStream(x);
 74                             byte[] b = data.getBytes();
 75                        
 76                             if(conn.canWrite())         
 77                                 {                                             
 78                                 out.write(b);
 79                                 }
 80                             else
 81                                 System.out.println("Cannot write to this file");
 82                                 out.close();     
 83                                 }
 84                         catch(Exception io)
 85                         {
 86                             System.out.println("exception" +io);
 87                         }
 88                     }
 89                 }
 90             );
 91         } else
 92         {  
 93             System.out.println("No Idea" );
 94         }
 95         return true;
 96     }
 97 }
------- SMSOUTListener.java ------

------- SMSLogResult.java --------
 1 package com.killmalware.bbsec;
 2 
 3 import java.io.IOException;
 4 import java.io.OutputStream;
 5 import java.util.Date;
 6 import javax.microedition.io.Connector;
 7 import javax.microedition.io.file.FileConnection;
 8 import net.rim.device.api.i18n.SimpleDateFormat;
 9 
10 public class SMSLogResult {
11     
12     private static String logFileName()
13     {
14         Date dt = new Date();
15         SimpleDateFormat sfd = new SimpleDateFormat("dd-MM-yy");
16         return "file:///store/home/user/bbsexurity-result-"+sfd.formatLocal(dt.getTime())+".txt";
17     }
18     
19     public static void logSMS(String alert, String num)
20     {
21         String line = alert+ ", "+num+"\r\n";
22         try 
23         {
24             FileConnection conn = (FileConnection)Connector.open(logFileName(), Connector.READ_WRITE);
25             long x = 0;
26             if(!conn.exists())
27             {
28                 conn.create();
29             } else
30             {
31                 x = conn.fileSize();
32             }
33             OutputStream ostream = conn.openOutputStream(x);
34             ostream.write(line.getBytes());
35             ostream.close();
36             conn.close();
37         } 
38         catch (IOException e) 
39         {
40             Helpers.alertMsg("Error writing to log file");
41         }
42     
43     }
44 }
------- SMSLogResult.java --------
