# FlashlightStudy
Android控制手电筒代码，简单易用，不需要任何权限。博客有详解：[https://blog.csdn.net/AlpinistWang/article/details/86772972](https://blog.csdn.net/AlpinistWang/article/details/86772972)

1.控制手电筒开关的工具类：

public class FlashUtils {
    private CameraManager manager;
    private Camera mCamera = null;
    private Context context;
    private boolean status = false;//记录手电筒状态

    FlashUtils(Context context){
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            manager = (CameraManager) context.getSystemService(Context.CAMERA_SERVICE);
        }
        this.context = context;
    }

    //打开手电筒
    public void open() {
        if(status){//如果已经是打开状态，不需要打开
            return;
        }
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            try {
                manager.setTorchMode("0", true);
            } catch (Exception e) {
                e.printStackTrace();
            }
        } else {
            PackageManager packageManager = context.getPackageManager();
            FeatureInfo[] features = packageManager.getSystemAvailableFeatures();
            for (FeatureInfo featureInfo : features) {
                if (PackageManager.FEATURE_CAMERA_FLASH.equals(featureInfo.name)) { // 判断设备是否支持闪光灯
                    if (null == mCamera) {
                        mCamera = Camera.open();
                    }
                    Camera.Parameters parameters = mCamera.getParameters();
                    parameters.setFlashMode(Camera.Parameters.FLASH_MODE_TORCH);
                    mCamera.setParameters(parameters);
                    mCamera.startPreview();
                }
            }
        }
        status = true;//记录手电筒状态为打开
    }

    //关闭手电筒
    public void close() {
        if(!status){//如果已经是关闭状态，不需要打开
            return;
        }
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            try {
                manager.setTorchMode("0", false);
            } catch (CameraAccessException e) {
                e.printStackTrace();
            }
        } else {
            if (mCamera != null) {
                mCamera.stopPreview();
                mCamera.release();
                mCamera = null;
            }
        }
        status = false;//记录手电筒状态为关闭
    }

    //改变手电筒状态
    public void converse(){
        if(status){
            close();
        }else{
            open();
        }
    }
}


2.使用方法：

FlashUtils utils = new FlashUtils(this);
        utils.open();//打开手电筒
//        utils.close();//关闭手电筒
