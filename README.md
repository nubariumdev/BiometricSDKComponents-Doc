# Biometric SDK for Android
Nubarium Biometrics Android SDK guides for developers.
[![GitHub Release](https://badgen.net/badge/release/v1.902/cyan)]()  


## SDK compatibility

- Starting Android 8.0 with API v24 or ABOVE.
- Mobile Android-based platforms.

## Installation

Install the Android SDK using Gradle.

### Prerequisites

- You must have your credentials or API Token. 
- Request your credentials at support.

### Install using Gradle

**Step 1: Declare repositories**
In the Project `build.gradle` file, declare the `jitpack` repository:

```kotlin
maven {
  url = uri("https://jitpack.io")
  credentials {
    // Your Access token
    username = "jp_akd345ksdhfdkfddfp"
    password = ""
  }
}
```


**Step 2: Add dependencies**
In the application `build.gradle` file, add the <u>latest Android SDK</u> package:

```kotlin
dependencies {
    // Get the latest version from Nubarium Biometrics SDK repository
  implementation("com.github.nubariumdev:BiometricSDKComponents:v1.902")
}
```

### Setting required permissions and also a third library dependency

Add the following permissions to `AndroidManifest.xml`:

AndroidManfiest.xml

```xml
<uses-feature android:name="android.hardware.camera" />
<uses-feature android:name="android.hardware.camera.autofocus" />

<uses-permission android:name="android.permission.CAMERA" android:required="true" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

```

### Known issues

#### Compatibility



## Integrate

### Before you begin

- You must install the Android SDK.
- Ensure that in your app `build.gradle` file, `applicationId`'s value (in the `defaultConfig` block) matches the app's app ID in Nubarium.
- Get the Nubarium Key or API Credentials. It is required to successfully initialize the SDK.
- The codes in this document are example implementations. Make sure to change the `<NUB_KEY>`, `<NUB_USERNAME>`,  `<NUB_PASSWORD>` and other placeholders as needed.
- All the steps in this document are mandatory unless stated otherwise.

## Initializing the Android SDK

It's recommended to initialize the SDK in the global Application class/subclass. 

###  Capture

#### **Step 1: Import Nubarium library**

In your Application class, import the Nubairum library classes:

```java
import com.nubarium.sdk.components.FaceResult;
import com.nubarium.sdk.components.FaceCapture;
```

#### **Step 2: Initialize the SDK**

**Local variables**

It requires to declare the component as local variable.

```java
private FaceCapture faceCapture;    // Face Capture component
```

In the global Application `onCreate`, create an instance of the component and set the credentials or API Key (either of the 2 methods can be used) and set the configuration.

```java
// Class initialization with the Application Context
faceCapture = new FaceCapture(this);

// Either of the 2 methods can be used, but only one.
// Set the credentials provided by Nubarium.
faceCapture.setCredentials(<NUB_USERNAME>,<NUB_PASSWORD>);

// Set the basic configuration (Options)
faceCapture.setShowPreview(false);   // Defaul values is false
faceCapture.setAntispoofing(true, FacialCapture.ANTISPOOFING_LEVEL_MEDIUM);
```

1. First, you have to set the Credentials or Api Key.
3. Then configure the behavior of the component.

* *setShowPreview* : Specifies whether the dialog requiring a confirmation with a preview photo is displayed.
  


**Customize messages (Optional)**

The messages and labels can be customized in your  `strings.xml` resource.

```xml
<string name="nbm_facial_title">Prueba de vida y Captura facial.</string>
<string name="nbm_facial_instructions">Para realizar la prueba de vida y la captura facial es necesario que enfoque la camara directamente en su rostro y evite moverse de manera brusca para un mejor enfoque.</string>
<string name="nbm_facial_instructions_list">Permanezca en un lugar iluminado.|Sostenga la camara en un ángulo de 90 grados.</string>

<string name="nbm_facial_preview_title">Confirmar</string>
<string name="nbm_facial_preview_instructions">Al realizar la validación tomamos una fotografía que usaremos en nuestro proceso de validación, si deseas, puedes reintentarlo oprimiendo en Reintentar, de lo contrarío sólo oprime el boton para Continuar.</string>

<string name="nbm_facial_btn_back">Regresar</string>
<string name="nbm_facial_btn_start">Iniciar</string>
<string name="nbm_facial_btn_accept">Aceptar.</string>
<string name="nbm_facial_btn_cancel">Cancelar</string>
<string name="nbm_facial_btn_validate">Validar</string>
<string name="nbm_facial_btn_finish">Finalizar</string>

<string name="nbm_facial_msg_dont_move">NO TE MUEVAS</string>
<string name="nbm_facial_msg_blurred">ENFOCA LA CAMARA Y NO SE MUEVA</string>
<string name="nbm_facial_msg_face_outside">COLOQUE SU ROSTRO DENTRO DEL OVALO</string>
<string name="nbm_facial_msg_no_face">COLOQUE SU ROSTRO FRENTE A LA CAMARA</string>
<string name="nbm_facial_msg_many_faces">ASEGURE QUE SU ROSTRO SEA EL UNICO</string>
<string name="nbm_facial_msg_far_away">ACERCATE UN POCO</string>
<string name="nbm_facial_msg_too_close">ALEJATE UN POCO</string>
<string name="nbm_facial_msg_fail">COLOQUESE EN UN LUGAR MEJOR ILUMINADO</string>
<string name="nbm_facial_msg_static_eye">ENFOQUE SU ROSTRO FRENTE A LA CAMARA</string>
<string name="nbm_facial_msg_captured">LISTO</string>
```

#### Step 3: **Setting up the Activity Result**

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        // Seeting the face component with the request and result code.
    faceCapture.process(requestCode, resultCode, data);
    super.onActivityResult(requestCode, resultCode, data);
}
```

It is recommended to use the initialization listener, to detect any fail or save the initialization token.

#### Step 4: Setting the initialization listener

```java
faceCapture.addOnInitListener(new FacialCapture.OnInitListener() {
    @Override
    public void onInit(String token) {
        // Saves the token in your local storage to reuse in case you need.
    }

    @Override
    public void onError(FacialCapture.Error error, String message) {
        // Track the erro of the initialization.
    }       
       
    @Override
    public void onFail(String reason) {
        // Track the reason of the initialization fail.
    }
});
```

#### Step 5: Setting a result listener

To receive the images and result of component execution it is necessary to setting up a result listener.

```java
faceCapture.addOnResultListener(new FacialCapture.OnResultListener() {

  @Override
  public void onSuccess(FaceResult faceResult, Bitmap faceImage, Bitmap areaImage) {
    
  }

  @Override
  public void onFail(FacialCapture.ReasonFail reasonFail, String reason) {

  }

  @Override
  public void onError(FacialCapture.Error error, String message) {

  }
});
```

- The `onSuccess()` callback method is invoked if the execution of the component was successful, the method returns the following elements.
  - faceResult : An instance of FaceResult with information like confidence and a attack indicator.
  - faceImage : A bitmap with the face cropped.
  - areaImage: A bitmap of the area where the face was framed
- The `onFail(String reason)` callback method is invoked when the liveness validation failed for the given configuration.
- The `onError(String error)` callback method is invoked when the component throws an error.

#### Step 5: Start component

As in the application the component is declared as a local variable, it can be started in programmatically or in some event such as onClick button.

***With Pre Initialization***

If you want to prevalidate your credentials and prevent a delay in the start event, just initialize the component after declare the properties and event listeners and before start.

```java
faceCapture.initialize();
```

But you can just call the event start.

```java
faceCapture.start();
```
