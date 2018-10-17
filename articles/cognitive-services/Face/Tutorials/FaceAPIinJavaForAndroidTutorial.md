---
title: 'Samouczek: wykrywanie i oznaczanie ramką twarzy na obrazie — interfejs API rozpoznawania twarzy, Java dla systemu Android'
titleSuffix: Azure Cognitive Services
description: W tym samouczku utworzysz prostą aplikację dla systemu Android, która korzysta z interfejsu API rozpoznawania twarzy do wykrywania i oznaczania ramką twarzy na obrazie.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: 50185588b77f01bced9462d5fd1ad67bb5de6e08
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129730"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Samouczek: tworzenie aplikacji dla systemu Android wykrywającej i oznaczającej ramką twarze na obrazie

W tym samouczku utworzysz prostą aplikację dla systemu Android, która korzysta z biblioteki klas języka Java usługi rozpoznawania twarzy do wykrywania i oznaczania ramką twarzy na obrazie. Aplikacja wyświetla wybrany obraz z każdą wykrytą twarzą otoczoną prostokątem. Kompletny przykładowy kod jest dostępny w witrynie GitHub na stronie z opisem [wykrywania i oznaczania ramką twarzy na obrazie w systemie Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample).

![Zrzut ekranu z systemu Android ze zdjęciem, na którym widać twarze otoczone czerwoną ramką](../Images/android_getstarted2.1.PNG)

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> - Tworzenie aplikacji systemu Android
> - Instalowanie biblioteki klienta usługi rozpoznawania twarzy
> - Korzystanie z biblioteki klienta do wykrywania twarzy na obrazie
> - Rysowanie ramki wokół każdej wykrytej twarzy

## <a name="prerequisites"></a>Wymagania wstępne

- Do uruchomienia przykładu potrzebny jest klucz subskrypcji. Klucze subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Program [Android Studio](https://developer.android.com/studio/) z minimum zestawem SDK 22 (wymaganym przez bibliotekę klienta rozpoznawania twarzy).
- Biblioteka klienta rozpoznawania twarzy [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) z narzędzia Maven. Pobieranie pakietu nie jest konieczne. Instrukcje instalacji znajdują się poniżej.

## <a name="create-the-project"></a>Tworzenie projektu

Utwórz projekt aplikacji dla systemu Android, wykonując następujące kroki:

1. Otwórz program Android Studio. W tym samouczku używany jest program Android Studio 3.1.
1. Wybierz pozycję **Start a new Android Studio project** (Utwórz nowy projekt programu Android Studio).
1. Na ekranie **Create Android Project** (Tworzenie projektu systemu Android) zmodyfikuj domyślną zawartość pól, jeśli to konieczne, a następnie kliknij przycisk **Next** (Dalej).
1. Na ekranie **Target Android Devices** (Docelowe urządzenia z systemem Android) użyj rozwijanego selektora, aby wybrać interfejs **API 22** lub wyższy, a następnie kliknij przycisk **Next** (Dalej).
1. Wybierz pozycję **Empty Activity** (Puste działanie), a następnie kliknij przycisk **Next** (Dalej).
1. Usuń zaznaczenie pola wyboru **Backwards Compatibility** (Zgodność z wcześniejszymi wersjami), a następnie kliknij przycisk **Finish** (Zakończ).

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Tworzenie interfejsu użytkownika do wybierania i wyświetlania obrazu

Otwórz plik *activity_main.xml*. Powinien zostać wyświetlony edytor układu Layout Editor. Wybierz kartę **Text** (Tekst), a następnie zastąp zawartość następującym kodem.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Otwórz plik *MainActivity.java*, po czym zastąp wszystko poza pierwszą instrukcją `package` następującym kodem.

Kod ustawi procedurę obsługi zdarzeń dla elementu `Button`, która uruchamia nowe działanie pozwalające użytkownikowi wybrać obraz. Po wybraniu obraz jest wyświetlany w elemencie `ImageView`.

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Teraz aplikacja umożliwia przeglądanie w poszukiwaniu zdjęcia i wyświetla je w oknie, podobnie jak na poniższej ilustracji.

![Zrzut ekranu z systemu Android przedstawiający zdjęcie z twarzami](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Konfigurowanie biblioteki klienta rozpoznawania twarzy

Interfejs API rozpoznawania twarzy jest interfejsem API w chmurze, który można wywoływać za pomocą żądań HTTPS. W tym samouczku używana jest biblioteka klienta rozpoznawania twarzy, która hermetyzuje te żądania internetowe w celu uproszczenia pracy.

W okienku **Project** (Projekt) wybierz za pomocą rozwijanego selektora pozycję **Android**. Rozwiń pozycję **Gradle Scripts** (Skrypty Gradle), a następnie otwórz plik *build.gradle (Module: app)*.

Dodaj zależność dla biblioteki klienta rozpoznawania twarzy, `com.microsoft.projectoxford:face:1.4.3`, jak pokazano na poniższym zrzucie ekranu, a następnie kliknij pozycję **Sync now** (Synchronizuj teraz).

![Zrzut ekranu z programu Android Studio przedstawiający plik build.gradle aplikacji](../Images/face-tut-java-gradle.png)

Otwórz plik **MainActivity.java** i dołącz następujące dyrektywy importu:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Dodawanie kodu biblioteki klienta rozpoznawania twarzy

Wstaw następujący kod w klasie `MainActivity`, powyżej metody `onCreate`:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Zastąp element `<API endpoint>` punktem końcowym interfejsu API, który został przypisany do Twojego klucza. Klucze subskrypcji bezpłatnej wersji próbnej są generowane w regionie **westcentralus**. Dlatego jeśli używasz klucza subskrypcji bezpłatnej wersji próbnej, instrukcja będzie wyglądać następująco:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Zastąp element `<Subscription Key>` kluczem subskrypcji. Na przykład:

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

W okienku **Project** (Projekt) rozwiń pozycje **app** i **manifests**, a następnie otwórz plik *AndroidManifest.xml*.

Wstaw następujący element jako bezpośredni element podrzędny elementu `manifest`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Skompiluj projekt, aby sprawdzić go pod kątem błędów. Teraz możesz przystąpić do wywoływania usługi rozpoznawania twarzy.

## <a name="upload-an-image-to-detect-faces"></a>Przekazywanie obrazu w celu wykrywania twarzy

Najprostszym sposobem wykrywania twarzy jest wywoływanie metody `FaceServiceClient.detect`. Ta metoda opakowuje metodę [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API i zwraca tablicę elementów `Face`.

Każdy zwrócony element `Face` zawiera prostokąt wskazujący lokalizację twarzy wraz z serią opcjonalnych atrybutów twarzy. W tym przykładzie potrzebne są same lokalizacje twarzy.

Jeśli wystąpi błąd, w oknie `AlertDialog` będzie wyświetlana jego przyczyna.

Wstaw następujące metody do klasy `MainActivity`.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Oznaczanie ramką twarzy na obrazie

Wstaw następującą metodę pomocniczą do klasy `MainActivity` Ta metoda rysuje prostokąt wokół każdej wykrytej twarzy.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

Uzupełnij metody `AsyncTask`, wskazane przez komentarz `TODO`, w metodzie `detectAndFrame`. W przypadku powodzenia wybrany obraz zostanie wyświetlony z ramkami wokół twarzy w elemencie `ImageView`.

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Na koniec w metodzie `onActivityResult` odkomentuj wywołanie metody `detectAndFrame`, jak pokazano poniżej.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Uruchamianie aplikacji

Uruchom aplikację i znajdź obraz z twarzą. Zaczekaj kilka sekund, aby umożliwić usłudze rozpoznawania twarzy udzielenie odpowiedzi. Po tym czasie otrzymasz wynik podobny do poniższego obrazu:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Podsumowanie

W tym samouczku przedstawiono podstawowy proces korzystania z usługi rozpoznawania twarzy i utworzono aplikację wyświetlającą oznaczone ramką twarze na obrazie.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wykrywania i używania charakterystycznych elementów twarzy.

> [!div class="nextstepaction"]
> [Jak wykrywać twarze na obrazie](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Poznaj interfejsy API rozpoznawania twarzy używane do wykrywania twarzy i ich atrybutów, takich jak położenie, płeć, wiek, pozycja głowy, zarost i okulary.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).