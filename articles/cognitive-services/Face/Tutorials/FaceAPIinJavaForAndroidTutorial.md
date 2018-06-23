---
title: Stają przed Java interfejsu API systemu Android samouczek | Dokumentacja firmy Microsoft
titleSuffix: Microsoft Cognitive Services
description: Tworzenie prostej aplikacji systemu Android używaną do wykrywania kognitywnych API krój usług i ludzkich ramki skierowany do obrazu.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349409"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Wprowadzenie do korzystania z powierzchni interfejsu API w języku Java Samouczek systemu Android

Z tego samouczka dowiesz się do tworzenia i utworzyć prostą aplikację systemu Android, która wywołuje API krój wykryć człowieka kroje obrazu. Aplikacja przedstawia wynik według ramek wykrytych powierzchni.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Przygotowanie

Aby korzystać z tego samouczka, będą potrzebne następujące wymagania wstępne:

- Android Studio i zainstalowany zestaw SDK
- Urządzenie z systemem android (opcjonalny w przypadku testowania).

## <a name="step1"></a>Krok 1: Subskrybowanie powierzchni interfejsu API i Pobierz klucz subskrypcji

Przed użyciem dowolnej powierzchni interfejsu API, musisz zapisać się do subskrybowania powierzchni interfejsu API w portalu usługi kognitywnych firmy Microsoft. Zobacz [subskrypcje](https://azure.microsoft.com/try/cognitive-services/). W tym samouczku można wykonać zarówno podstawowego i pomocniczego klucza.

## <a name="step2"></a>Krok 2: Tworzenie struktury aplikacji

W tym kroku zostanie utworzony projekt aplikacji systemu Android, aby zaimplementować podstawowy interfejs użytkownika do zbierania i wyświetlania obrazu. Po prostu postępuj zgodnie z instrukcjami poniżej: 

1. Otwórz program Android Studio.
2. W menu Plik kliknij **nowy projekt...**
3. Nazwa aplikacji **MyFirstApp**, a następnie kliknij przycisk Dalej. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Wybierz platformę docelową zgodnie z wymaganiami, a następnie kliknij przycisk Dalej. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Wybierz **podstawowe działania** , a następnie kliknij przycisk Dalej.
6. Nazwy w następujący sposób działania, a następnie kliknij przycisk Zakończ. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Otwórz **activity_main.xml**, powinien zostać wyświetlony w edytorze układ tego działania.
8. Wyświetlanie tekstu pliku źródłowego, a następnie Edytuj układ działania w następujący sposób:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Otwórz **MainActivity.java** i Wstaw następujące dyrektywy importu na początku pliku:

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
    ```
      
    Po drugie zmodyfikuj klasy w następujący sposób:  
    
    ```java
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
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Teraz aplikacji można przeglądać zdjęcia z galerii i wyświetl ją w oknie, podobnie jak na poniższym obrazie:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Krok 3: Konfigurowanie biblioteki klienckiej powierzchni interfejsu API

Interfejs API krój jest chmurę żądań interfejsu API, który można wywołać przy użyciu protokołu HTTPS. Dla wygodny sposób korzystania z interfejsu API krój w aplikacjach platformy .NET biblioteki klienta jest również udostępniany, aby hermetyzować sieci web żądania. W tym przykładzie używamy biblioteki klienckiej uprościć pracę. 

Postępuj zgodnie z instrukcjami poniżej, aby skonfigurować biblioteki klienta: 

1. Zlokalizuj najwyższego poziomu **build.gradle** pliku projektu z panelu Projekt pokazano w przykładzie. Należy pamiętać, że jest kilka innych **build.gradle** pliki w drzewie z projektu i trzeba otworzyć najwyższego poziomu **build.gradle** pliku na początku.
2. Dodaj **mavenCentral()** do repozytoriów swoje projekty. Można również użyć jcenter(), która jest repozytorium domyślnego programu Android Studio, ponieważ jcenter() jest nadzbiorem mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Otwórz **build.gradle** pliku w projekcie "aplikacji".
4. Dodaj zależność nasza Biblioteka klienta przechowywanych w centralnym repozytorium Maven:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Otwórz **MainActivity.java** w projekcie 'app' i wstawiania następujące zaimportować dyrektywy: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Następnie wstaw następujący kod w klasie:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   Pierwszy parametr powyżej Zastąp punkt końcowy interfejsu API, który został przypisany do klucza w kroku 1. Na przykład:
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Drugi parametr Zamień na klucz subskrypcji, które zostały uzyskane w kroku 1.
   
6. Otwórz plik **AndroidManifest.xml** w projekcie "aplikacji". Wstaw następujący element jako element podrzędny **manifestu** elementu:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Teraz można przystąpić do wywołania interfejsu API krojów z aplikacji. 

## <a name="step4"></a>Krok 4: Przekaż obrazy do wykrywania powierzchni

Najbardziej oczywistym sposobem wykryć kroje jest przez wywołanie metody [stają przed — wykrywanie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) interfejsu API, przekazując bezpośrednio do pliku obrazu. Podczas korzystania z biblioteki klienta, można to zrobić za pomocą metody asynchronicznej **DetectAsync** z **FaceServiceClient** klasy. Każdego zwróconego krój zawiera prostokąt wskazująca lokalizacji, w połączeniu z serii krój opcjonalne atrybuty. W tym przykładzie tylko musimy pobrać lokalizacji twarzy na obrazie. Tutaj należy wstawić metody do **MainActivity** klasy do wykrywania twarzy na obrazie: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
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
```

## <a name="step5"></a>Krok 5: Znaku skierowany w obrazie

W ostatnim kroku możemy połączyć ze sobą wszystkie powyższe kroki i oznacz wykryto krojów z ramek na obrazie. Najpierw otwórz **MainActivity.java** i Wstaw metody pomocnika Rysowanie prostokątów: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
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

Teraz zakończyć części TODO **detectAndFrame** metody ramki kroje w celu zgłoszenia stanu.

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
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Na koniec należy dodać wywołanie **detectAndFrame** metody z **onActivityResult** metody, jak pokazano poniżej. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Uruchomienie tej aplikacji i wyszukaj obraz zawierającego powierzchnię. Zaczekaj kilka sekund, chmury interfejsu API na potrzeby odpowiedzi. Po wykonaniu tej otrzymasz wynik podobnie jak obraz poniżej: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Podsumowanie

W tym samouczku przedstawiono przebieg przy użyciu interfejsu API krój i utworzyć aplikację w celu wyświetlania twarzy obrazy. Aby uzyskać więcej informacji na temat powierzchni interfejsu API można znaleźć instrukcje i [dokumentacja interfejsu API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Samouczki pokrewne

- [Wprowadzenie do korzystania z powierzchni interfejsu API w samouczku CSharp](FaceAPIinCSharpTutorial.md)
- [Wprowadzenie do korzystania z powierzchni interfejsu API w samouczku Python](FaceAPIinPythonTutorial.md)
