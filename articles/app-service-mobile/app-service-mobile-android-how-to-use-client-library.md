---
title: Jak używać zestawu SDK aplikacji usługi Azure Mobile dla systemu Android | Dokumentacja firmy Microsoft
description: Jak używać zestawu SDK aplikacji usługi Azure Mobile dla systemu Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/07/2019
ms.author: crdun
ms.openlocfilehash: 45b5ac0c9b3535e5cc5efdc6827d694b41e0b8dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859396"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak używać zestawu SDK aplikacji usługi Azure Mobile dla systemu Android

Ten przewodnik pokazuje, jak kliencką dla systemu Android SDK dla aplikacji mobilnych umożliwiają implementowanie typowych scenariuszy, takich jak:

* Wykonywanie zapytania dla danych (Wstawianie, aktualizowanie i usuwanie).
* uwierzytelnianie.
* Obsługa błędów.
* Dostosowywanie klienta.

Ten przewodnik koncentruje się na zestaw SDK systemu Android po stronie klienta.  Aby dowiedzieć się więcej na temat zestawów SDK po stronie serwera dla aplikacji mobilnych, zobacz [pracy z zapleczem platformy .NET SDK] [ 10] lub [jak do użycia zaplecza Node.js SDK][11].

## <a name="reference-documentation"></a>Dokumentacja referencyjna

Możesz znaleźć [dokumentacja interfejsu API Javadocs] [ 12] dla biblioteki klienckiej systemu Android w witrynie GitHub.

## <a name="supported-platforms"></a>Obsługiwane platformy

Azure Mobile Apps SDK dla systemu Android obsługuje poziomy interfejsu API 19-24 (KitKat za pośrednictwem określić wersję Nougat) standardy telefony i tablety.  Uwierzytelnianie, w szczególności korzysta z typowym podejściem framework sieci web w celu zbierania poświadczeń.  Uwierzytelnianie serwera przepływu nie działa z niewielkich współczynnik urządzeń, takich jak czujki.

## <a name="setup-and-prerequisites"></a>Instalacji i wymagania wstępne

Wykonaj [Mobile Apps — Szybki Start](app-service-mobile-android-get-started.md) samouczka.  To zadanie gwarantuje, że zostały spełnione wszystkie wymagania wstępne dotyczące tworzenia usługi Azure Mobile Apps.  Samouczek Szybki Start pomaga również skonfigurować swoje konto i Utwórz pierwsze zaplecze aplikacji mobilnej.

Jeśli zdecydujesz się na ukończenie tego samouczka Szybki Start, wykonaj następujące zadania:

* [Tworzenie zaplecza aplikacji mobilnej] [ 13] za pomocą aplikacji dla systemu Android.
* W programie Android Studio [pliki kompilacji aktualizacji Gradle](#gradle-build).
* [Włącz uprawnień internetowych](#enable-internet).

### <a name="gradle-build"></a>Zaktualizuj plik kompilacji narzędzia Gradle

Zmień **build.gradle** plików:

1. Dodaj następujący kod do *projektu* poziom **build.gradle** pliku:

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Dodaj następujący kod do *aplikacji modułu* poziom **build.gradle** pliku wewnątrz *zależności* tag:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Obecnie najnowsza wersja to 3.4.0. Obsługiwane wersje są wymienione [w serwisie bintray][14].

### <a name="enable-internet"></a>Włącz uprawnień internetowych

Aby uzyskać dostęp do platformy Azure, aplikacji musi mieć włączone uprawnienie INTERNET. Jeśli jeszcze nie jest włączona, Dodaj następujący wiersz kodu, aby Twoje **AndroidManifest.xml** pliku:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Tworzenie połączenia klienta

Usługa Azure Mobile Apps udostępnia cztery funkcje dla twojej aplikacji mobilnej:

* Dostęp do danych i synchronizacji z usługą mobilną systemu Azure aplikacje w trybie Offline.
* Wywoływanie niestandardowych interfejsów API napisanych przy użyciu zestawu SDK serwera usługi Azure Mobile Apps.
* Uwierzytelnianie za pomocą usługi Azure App Service, uwierzytelniania i autoryzacji.
* Push rejestracji powiadomień z usługą Notification Hubs.

Każda z tych funkcji najpierw wymaga utworzenia `MobileServiceClient` obiektu.  Tylko jeden `MobileServiceClient` obiekt powinien zostać utworzony w ramach Twojego klienta mobilnego (oznacza to, należy go wzorzec Singleton).  Aby utworzyć `MobileServiceClient` obiektu:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` To ciąg lub obiekt adresu URL, który wskazuje na zapleczu mobilnym.  Jeśli używasz usługi Azure App Service do hostowania usługi zaplecza aplikacji mobilnych, upewnij się, używasz bezpiecznego `https://` wersję adresu URL.

Klient wymaga również dostęp do działań lub kontekst — `this` parametru w przykładzie.  Konstrukcja MobileServiceClient powinno mieć miejsce, w ramach `onCreate()` odwołuje do metody działania `AndroidManifest.xml` pliku.

Najlepszym rozwiązaniem należy abstrakcyjna komunikacji z serwerem w jej własnej klasy (wzorzec singleton).  W takim przypadku należy przekazać działań w Konstruktorze odpowiednio skonfigurować usługi.  Na przykład:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Teraz można wywołać `AzureServiceAdapter.Initialize(this);` w `onCreate()` metody działania głównego.  Inne metody potrzeby uzyskiwania dostępu do używania klienta `AzureServiceAdapter.getInstance();` uzyskać odwołanie z kartą usługi.

## <a name="data-operations"></a>Operacje na danych

Podstawowy zestaw SDK usług Azure Mobile Apps jest zapewnienie dostępu do danych przechowywanych w ramach usługi SQL Azure na zaplecza aplikacji mobilnej.  Dostęp tych danych za pomocą silnie typizowanej klasy (preferowany) lub wykonywania kwerend (niezalecane).  Duża część tej sekcji zajmuje się za pomocą silnie typizowanej klasy.

### <a name="define-client-data-classes"></a>Definiowanie klas danych klienta

Dostęp do danych z tabel SQL Azure, definiowania klas danych klienta, które odpowiadają z tabelami w wewnętrznej bazie danych aplikacji mobilnej. Przykłady w tym temacie przyjęto założenie, tabela o nazwie **MyDataTable**, która zawiera następujące kolumny:

* id
* tekst
* Wykonaj

Odpowiedni typizowany obiekt po stronie klienta znajdują się w pliku o nazwie **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Dodaj metody getter i setter dla każdego pola, które można dodać.  Jeśli tabela SQL Azure zawiera więcej kolumn, należy dodać odpowiednich pól do tej klasy.  Na przykład jeśli obiekt DTO (data transfer object) ma priorytet kolumna liczb całkowitych, a następnie można dodać tego pola, wraz z jego metody getter i setter:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Aby dowiedzieć się, jak utworzyć dodatkowe tabele w kodzie zaplecza funkcji Mobile Apps, zobacz [jak: Zdefiniuj kontrolera tabeli] [ 15] (zaplecze .NET) lub [Definiowanie tabel przy użyciu schematu dynamicznego] [ 16] (zaplecze środowiska Node.js).

Tabeli wewnętrznej bazy danych usługi Azure Mobile Apps definiuje pięć specjalne pola, cztery z nich są dostępne dla klientów:

* `String id`: Globalnie unikatowy identyfikator rekordu.  Najlepszym rozwiązaniem jest wprowadzić identyfikator reprezentację ciągu [UUID] [ 17] obiektu.
* `DateTimeOffset updatedAt`: Data/godzina ostatniej aktualizacji.  Pole updatedAt jest ustawiana przez serwer i nigdy nie powinna być ustawiona przez kod klienta.
* `DateTimeOffset createdAt`: Data/godzina utworzenia obiektu.  Pole createdAt jest ustawiana przez serwer i nigdy nie powinna być ustawiona przez kod klienta.
* `byte[] version`: Zwykle reprezentowany jako ciąg znaków, wersja jest również ustawiona przez serwer.
* `boolean deleted`: Wskazuje, że rekord została usunięta, ale nie przeczyszczono jeszcze.  Nie używaj `deleted` jako właściwość w klasie.

Pole `id` jest wymagane.  `updatedAt` Pola i `version` pola są używane do synchronizacji w trybie offline (dla przyrostowych synchronizacji i konflikt rozpoznawania odpowiednio).  `createdAt` Pole jest polem Odwołanie i nie jest używany przez klienta.  Nazwy są "w locie" nazw właściwości i nie są zmieniane.  Jednak można utworzyć mapowanie między obiektu oraz nazwy "w locie", przy użyciu [gson] [ 3] biblioteki.  Na przykład:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Tworzenie odwołania do tabeli

Aby uzyskać dostęp do tabeli, należy najpierw utworzyć [MobileServiceTable] [ 8] obiektu przez wywołanie metody **getTable** metody [MobileServiceClient] [9].  Ta metoda ma dwa przeciążenia:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

W poniższym kodzie **element mClient** jest odwołaniem do obiektu MobileServiceClient.  Pierwsze przeciążenie jest używany, gdy nazwę klasy i nazwy tabeli są takie same, a ten służy w przewodniku Szybki Start:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Drugie przeciążenie jest używany, gdy nazwa tabeli jest inny niż nazwa klasy: pierwszy parametr jest nazwą tabeli.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Wysłać zapytanie do tabeli wewnętrznej bazy danych

Najpierw Uzyskaj odwołanie do tabeli.  Następnie można wykonać zapytania w odwołaniu do tabeli.  Zapytanie jest dowolnej kombinacji:

* A `.where()` [klauzuli filtru](#filtering).
* `.orderBy()` [Klauzula ordering](#sorting).
* A `.select()` [pola wyboru klauzuli](#selection).
* A `.skip()` i `.top()` dla [stronicowanych wyników](#paging).

Klauzule muszą być przedstawione w poprzednich kolejności.

### <a name="filter"></a> Filtrowanie wyników

Ogólna postać zapytania jest:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Powyższy przykład zwraca wszystkie wyniki (maksymalnie maksymalny rozmiar strony ustawiony przez serwer).  `.execute()` Metoda wykonuje zapytanie do wewnętrznej bazy danych.  Zapytanie jest konwertowany na [OData v3] [ 19] zapytania przed ich przesłaniem do zaplecza usługi Mobile Apps.  Po otrzymaniu zaplecze funkcji Mobile Apps konwertuje zapytania do instrukcji SQL przed wykonaniem go na wystąpienie usług SQL Azure.  Ponieważ aktywności sieciowej dopiero po pewnym czasie `.execute()` metoda zwraca [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtruj dane zwrotne

Następujące wykonywania zapytania zwraca wszystkie elementy z **ToDoItem** tabeli gdzie **pełną** jest równa **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** jest odwołanie do tabeli usługi mobilnej, która utworzony wcześniej.

Zdefiniowanie filtru używającego **gdzie** wywołania metody odwołania do tabeli. **Gdzie** metody następuje **pola** metody następuje metodę, która określa logiczną predykat. Możliwe metody predykatu obejmują **eq** (równe) **ne** (nie równa się), **gt** (większe niż) **ge** (większe niż lub równe) **lt** (mniejsze niż), **le** (mniejsze niż lub równe). Te metody umożliwiają porównanie pola numeru i ciąg do konkretnych wartości.

Można filtrować według daty. Poniższe metody pozwalają porównać pole daty całego lub części daty: **roku**, **miesiąca**, **dzień**, **godzinę**,  **minuty**, i **drugi**. Poniższy przykład umożliwia dodanie filtru dla elementów którego *termin* jest równe 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Następujące metody obsługi złożonych filtrów na pól ciągów: **startsWith**, **endsWith**, **concat**, **podciąg**, **indexOf**, **Zastąp**, **toLower**, **toUpper**, **trim**, i **długość** . Następujące przykładowe filtry dla tabeli wiersze, w których *tekstu* kolumny zaczyna się od "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Następujących metod operatorów są obsługiwane w numerów pól: **Dodaj**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling**, i **zaokrąglanie**. Następujące przykładowe filtry dla tabeli wiersze, w których **czas trwania** jest liczbą parzystą.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Predykaty można połączyć z tych metod logiczne: **i**, **lub** i **nie**. Poniższy przykład łączy dwie z powyższych przykładach.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Operatory logiczne grupy i zagnieżdżonych:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Aby uzyskać bardziej szczegółowe dyskusji i przykłady, filtrowania, zobacz [Eksplorowanie bogactwa model zapytań klienta systemu Android][20].

### <a name="sorting"></a>Sortuj zwrócone dane

Poniższy kod zwraca wszystkie elementy z tabeli **ToDoItems** posortowane rosnąco *tekstu* pola. *mToDoTable* jest odwołanie do tabeli wewnętrznej bazy danych, który został utworzony wcześniej:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Pierwszy parametr **orderBy** metodą jest taka sama jak nazwa pola do sortowania ciągu. Drugi parametr używa **QueryOrder** wyliczeniu, aby określić, czy mają być sortowane w kolejności rosnącej lub malejącej.  Jeśli używasz filtrowania, za pomocą ***gdzie*** metody ***gdzie*** metoda musi być wywoływana przed ***orderBy*** metody.

### <a name="selection"></a>Wybrać określone kolumny

Poniższy kod ilustruje sposób zwracania wszystkich elementów z tabeli **ToDoItems**, ale wyświetlane są tylko **pełną** i **tekstu** pola. **mToDoTable** jest odwołaniem do utworzonej wcześniej tabeli wewnętrznej bazy danych.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry funkcji wybierz są ciąg nazwy kolumn w tabeli, które mają być zwracane.  **Wybierz** metoda musi postępuj zgodnie z metod, takich jak **gdzie** i **orderBy**. Można wykonać za pomocą metod stronicowania, takich jak **pominąć** i **górnej**.

### <a name="paging"></a>Zwróć dane na stronach

Dane są **zawsze** zwrócony na stronach.  Maksymalną liczbę rekordów zwróconych jest ustawiony przez serwer.  Jeśli klient żąda więcej rekordów, serwer zwraca maksymalną liczbę rekordów.  Domyślnie maksymalny rozmiar strony na serwerze jest 50 rekordów.

Pierwszy przykład pokazuje sposób wybierania pierwszych pięć elementów z tabeli. Zwraca elementy z tabeli **ToDoItems**. **mToDoTable** jest odwołanie do tabeli wewnętrznej bazy danych, który został utworzony wcześniej:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Oto zapytanie, które pomija pierwszych pięć elementów, a następnie zwraca następny 5:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Jeśli chcesz pobrać wszystkie rekordy w tabeli, należy zaimplementować kod, aby przejść przez wszystkie strony:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Żądania dla wszystkich rekordów przy użyciu tej metody powoduje utworzenie co najmniej dwa żądania do zaplecza usługi Mobile Apps.

> [!TIP]
> Wybieranie rozmiaru prawej strony jest kompromis między użycie pamięci, gdy żądanie się dzieje, przepustowości i opóźnienia w całkowicie odbiera dane.  Wartość domyślna (50 rekordów) jest odpowiednia dla wszystkich urządzeń.  W przypadku wyłącznie działają na większych urządzeniach pamięci, należy zwiększyć maksymalnie 500.  Znaleźliśmy, że zwiększenie rozmiaru strony ponad 500 rekordów skutkuje niedopuszczalne opóźnienia i problemy z dużej ilości pamięci.

### <a name="chaining"></a>Jak: Łączenie metody zapytań

Metody stosowane w tworzenie zapytań o tabele wewnętrznej bazy danych mogą być łączone. Łańcucha metody zapytań pozwala wybrać określone kolumny przefiltrowano wiersze, które są sortowane i stronicowanej. Można tworzyć złożone filtry logiczne.  Każda metoda zapytanie zwraca obiekt zapytania. Aby zakończyć szereg metod i faktycznie uruchomić zapytanie, należy wywołać **wykonania** metody. Na przykład:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Metody zapytań łańcuchowych muszą być uporządkowane w następujący sposób:

1. Filtrowanie (**gdzie**) metody.
2. Sortowanie (**orderBy**) metody.
3. Wybór (**wybierz**) metody.
4. stronicowanie (**pominąć** i **górnej**) metody.

## <a name="binding"></a>Powiąż dane z interfejsu użytkownika

Powiązanie danych obejmuje trzy składniki:

* Źródło danych
* Układ ekranu
* Karty, która łączy ze sobą dwa.

W naszym przykładowym kodzie zostanie zwrócona dane z tabeli Mobile Apps SQL Azure **ToDoItem** do tablicy. To działanie jest typowy wzorzec dla aplikacji przetwarzających dane.  Zapytania bazy danych często zwracają zbiór wierszy, które klient pobiera listy lub tablicy. W tym przykładzie tablica jest źródło danych.  Kod ten określa układ ekranu, który definiuje widoku danych, który pojawia się na urządzeniu.  Dwa są powiązane wraz z adapter, czyli w tym kodzie rozszerzenia z **ArrayAdapter&lt;ToDoItem&gt;**  klasy.

#### <a name="layout"></a>Definiowanie układu

Układ jest definiowany przez kilka fragmentów kodu XML. Biorąc pod uwagę istniejącego układu, poniższy kod przedstawia **ListView** ma zostać wypełniony przy użyciu naszych danych serwera.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

W poprzednim kodzie *listitem* atrybut określa identyfikator układ dla pojedynczego wiersza na liście. Ten kod określa pole wyboru i jego tekst i pobiera jednorazowego dla każdego elementu na liście. Nie są wyświetlane w tym układzie **identyfikator** pola i bardziej złożonych układ określić dodatkowych pól na ekranie. Ten kod znajduje się w **row_list_to_do.xml** pliku.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Zdefiniuj karty
Ponieważ naszych widoku źródła danych jest tablicą **ToDoItem**, firma Microsoft podklasy naszej karty z **ArrayAdapter&lt;ToDoItem&gt;**  klasy. Tej podklasy generuje widok dla każdego **ToDoItem** przy użyciu **row_list_to_do** układu.  W naszym kodzie definiujemy następujące klasy, która jest rozszerzeniem **ArrayAdapter&lt;E&gt;**  klasy:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Zastąp kart **getView** metody. Na przykład:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Możemy utworzyć wystąpienie tej klasy w naszych działań w następujący sposób:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Drugi parametr do konstruktora ToDoItemAdapter jest odwołanie do układu. Firma Microsoft może teraz utworzyć wystąpienie **ListView** i przypisz kartę, która ma **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Użyj karty do powiązania w interfejsie użytkownika

Teraz można przystąpić do użycia powiązanie danych. Poniższy kod ilustruje sposób uzyskania elementów w tabeli i wypełnia karta lokalna zwróconych elementów.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Wywołania adapter kiedykolwiek zmodyfikujesz **ToDoItem** tabeli. Ponieważ zmiany są wykonywane na podstawie rekordu na podstawie, możesz obsługiwać pojedynczy wiersz zamiast kolekcji. Podczas wstawiania elementu wywołania **Dodaj** metoda karty; podczas usuwania, należy wywołać **Usuń** metody.

Możesz znaleźć pełny przykład w [projekt dla systemu Android Przewodnik Szybki Start][21].

## <a name="inserting"></a>Wstawianie danych do wewnętrznej bazy danych

Tworzy wystąpienie elementu *ToDoItem* klasy i ustaw jego właściwości.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Następnie użyj **insert()** do wstawienia obiektu:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Dopasowuje zwróconą jednostkę danych wstawione do tabeli wewnętrznej bazy danych są uwzględnione identyfikator i innych wartości (takich jak `createdAt`, `updatedAt`, i `version` pól) Ustaw do wewnętrznej bazy danych.

Tabele Mobile Apps, wymagają kolumna klucza podstawowego o nazwie **identyfikator**. Ta kolumna musi być ciągiem. Wartość domyślna w kolumnie identyfikator jest identyfikatorem GUID.  Możesz podać inne unikatowe wartości, takie jak adresy e-mail lub nazwy użytkowników. Wartość Identyfikatora ciągu nie jest dostępna do wstawionego rekordu, zaplecze generuje nowy identyfikator GUID.

Wartości Identyfikatora ciągu zapewniają następujące korzyści:

* Identyfikatory mogą być generowane, bez konieczności szukania komunikacji dwustronnej w bazie danych.
* Rekordy są łatwiejsze do scalenia z różnych tabel lub baz danych.
* Wartości Identyfikatora lepszej integracji za pomocą aplikacji logiki.

Wartości Identyfikatora ciągu są **wymagane** obsługę synchronizacji w trybie offline.  Nie można zmienić identyfikatora, gdy jest on przechowywany w bazie danych zaplecza.

## <a name="updating"></a>Aktualizowanie danych w aplikacji mobilnej

Aby zaktualizować dane w tabeli, należy przekazać nowy obiekt do **update()** metody.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

W tym przykładzie *elementu* jest odwołanie do wiersza w *ToDoItem* tabeli, która miała niektóre zmiany wprowadzone do niego.  Wiersz o takim samym **identyfikator** jest aktualizowana.

## <a name="deleting"></a>Usuń dane w aplikacji mobilnej

Poniższy kod pokazuje, jak usunąć dane z tabeli, określając obiektu danych.

```java
mToDoTable
    .delete(item);
```

Można również usunąć element, określając **identyfikator** pola wiersza do usunięcia.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Wyszukiwanie określonego elementu według identyfikatora

Wyszukaj element z określonym **identyfikator** pole **lookUp()** metody:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Jak: Praca z danymi bez typu

Nietypizowane model programowania zapewnia dokładną kontrolę nad serializacji JSON.  Istnieje kilka typowych scenariuszy, w których warto korzystać z modelu programowania bez typu. Jeśli na przykład tabeli wewnętrznej bazy danych zawiera wiele kolumn, należy odwoływać się do podzestawu kolumn.  Typizowany model wymaga zdefiniowania wszystkich kolumn, które są zdefiniowane w zaplecza funkcji Mobile Apps w klasie danych.  Większość wywołań interfejsu API do uzyskiwania dostępu do danych są podobne do typizowanych wywołania programowania. Główną różnicą jest to, że w modelu danych można wywoływać metody na **MobileServiceJsonTable** obiektu, zamiast **MobileServiceTable** obiektu.

### <a name="json_instance"></a>Utwórz wystąpienie obiektu tabeli danych

Podobnie jak typizowany model, możesz uruchomić pobieranie odwołania do tabeli, ale w tym przypadku jest **MobileServicesJsonTable** obiektu. Uzyskaj odwołanie, wywołując **getTable** metody na instancji klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po utworzeniu wystąpienia **MobileServiceJsonTable**, praktycznie ma tego samego interfejsu API dostępne jako typizowany model programowania. W niektórych przypadkach metody przyjmują parametru bez typu, a nie typizowany parametr.

### <a name="json_insert"></a>Wstaw do tabeli bez typu
Poniższy kod przedstawia sposób wykonywania instrukcji insert. Pierwszym krokiem jest utworzenie [JsonObject][1], który jest częścią [gson] [ 3] biblioteki.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Następnie należy użyć **insert()** można wstawić obiektu danych do tabeli.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Aby uzyskać identyfikator obiektu wstawiono, należy użyć **getAsJsonPrimitive()** metody.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Usuń z tabeli bez typu
Poniższy kod przedstawia sposób usunąć wystąpienie, w tym przypadku to samo wystąpienie elementu **JsonObject** utworzonej wcześniej *Wstaw* przykład. Kod jest taki sam, podobnie jak w przypadku wpisane, ale metoda ma inny podpis, ponieważ odwołuje się do **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Możesz także usunąć wystąpienie bezpośrednio za pomocą jego Identyfikatora:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Zwracanie wszystkich wierszy z tabeli bez typu
Poniższy kod przedstawia sposób pobierania całą tabelę. Ponieważ używasz tabeli JSON można selektywnie można pobierać tylko niektóre kolumny tabeli.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Ten sam zestaw filtrowania, filtrowanie i stronicowanie metod, które są dostępne dla typizowanego modelu są dostępne dla modelu danych.

## <a name="offline-sync"></a>Implementowanie synchronizacji w trybie Offline

Zestaw SDK klienta usługi Azure Mobile Apps implementuje również synchronizacji danych w trybie offline przy użyciu bazy danych SQLite, aby przechowywać kopię dane serwera lokalnie.  Operacje wykonywane na tabelę w trybie offline nie wymagają łączności mobilnej do pracy.  Synchronizacja w trybie offline ułatwia odporność i wydajność kosztem bardziej złożona logika do rozwiązywania konfliktów.  Zestaw SDK klienta usługi Azure Mobile Apps implementuje następujące funkcje:

* Synchronizacja przyrostowa: Tylko zaktualizowanych i nowych rekordów są pobierane, zapisywanie zużycie przepustowości i pamięci.
* Optymistyczna współbieżność: Przyjęto założenie, że operacje są poprawne.  Rozwiązywanie konfliktów jest odroczone do czasu aktualizacje są wykonywane na serwerze.
* Rozwiązywanie konfliktów: Zestaw SDK wykrywa zmiany powodujące konflikt stało się na serwerze i zawiera punkty zaczepienia, aby ostrzec użytkownika.
* Soft Delete: Usunięte rekordy są oznaczane usuniętych, dzięki czemu inne urządzenia, aby zaktualizować ich pamięci podręcznej offline.

### <a name="initialize-offline-sync"></a>Inicjowanie synchronizacji w trybie Offline

Każda tabela w trybie offline, musi być zdefiniowany w pamięci podręcznej offline przed użyciem.  Normalnie definicja tabeli jest wykonywane natychmiast po utworzeniu klienta:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Uzyskaj odwołanie do tabeli pamięci podręcznej w trybie Offline

W tabeli usługi online, należy użyć `.getTable()`.  Do tabeli w trybie offline, należy użyć `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Wszystkie metody, które są dostępne dla usługi online tabel (w tym filtrowanie, sortowanie, stronicowanie, wstawianie danych, aktualizowanie danych i usunięcie danych) działa równie dobrze w tabelach w trybie online i offline.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizowanie lokalnej pamięci podręcznej w trybie Offline

Synchronizacja jest w ramach kontroli aplikacji.  Oto przykładowa metoda synchronizacji:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Jeśli nie podano nazwy zapytania do `.pull(query, queryname)` metody, a następnie synchronizacja przyrostowa służy do zwrócić tylko rekordy, które zostały utworzone lub zmienione od ostatniego pomyślnie ukończono ściągnięcia.

### <a name="handle-conflicts-during-offline-synchronization"></a>Postępowanie w przypadku konfliktów podczas synchronizacji w trybie Offline

Jeśli konflikt występuje podczas `.push()` operacji `MobileServiceConflictException` zgłaszany.   Element wystawiony przez serwer jest osadzony w wyjątku i mogą być pobierane według `.getItem()` w drodze wyjątku.  Dopasuj wypychania przez wywołanie metody następujących elementów w obiekcie MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Po wszystkich konfliktów są oznaczone jako użytkownik chce, wywołaj `.push()` ponownie, aby rozwiązać wszystkie konflikty.

## <a name="custom-api"></a>Wywoływanie niestandardowego interfejsu API

Niestandardowy interfejs API umożliwia zdefiniowanie niestandardowe punkty końcowe, które udostępniają funkcje serwera, które nie mapowanie do wstawiania, aktualizowania, usuwania lub operacja odczytu. Korzystając z niestandardowego interfejsu API, może mieć większą kontrolę nad wiadomości, w tym odczytywanie i ustawiania nagłówków wiadomości HTTP oraz definiowania format treści wiadomości, innej niż JSON.

Na kliencie dla systemu Android, należy wywołać **invokeApi** metody do wywoływania niestandardowego punktu końcowego interfejsu API. Poniższy przykład pokazuje, jak wywołać punkt końcowy interfejsu API o nazwie **completeAll**, która zwraca klasę kolekcji o nazwie **MarkAllResult**.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

**InvokeApi** metoda jest wywoływana na kliencie, który wysyła żądanie POST do nowego, niestandardowego interfejsu API. Wynik zwracany przez niestandardowy interfejs API jest wyświetlany w oknie dialogowym komunikatu, ponieważ są błędy. Inne wersje **invokeApi** pozwalają opcjonalnie wysyłać obiekt w treści żądania, określ metodę HTTP i Wyślij parametry zapytania wraz z żądaniem. Bez typu wersje **invokeApi** znajdują się również.

## <a name="authentication"></a>Dodawanie uwierzytelniania do aplikacji

Samouczki już szczegółowego opisywania, jak dodać te funkcje.

Usługa App Service obsługuje [uwierzytelnianie użytkowników aplikacji](app-service-mobile-android-get-started-users.md) za pomocą różnych dostawców tożsamości zewnętrznych: Facebook, Google, konta Microsoft, Twitter i Azure Active Directory. Możesz ustawić uprawnienia w tabelach ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników. Tożsamość uwierzytelnionych użytkowników umożliwia również zaimplementować reguły autoryzacji w zapleczu.

Obsługiwane są dwa przepływy uwierzytelniania: **serwera** przepływu i **klienta** przepływu. Przepływ serwera zapewnia najprostszą proces uwierzytelniania opiera się na interfejsie sieci web dostawcy tożsamości.  Nie dodatkowe zestawy SDK są wymagane do zaimplementowania uwierzytelniania przepływu serwera. Uwierzytelnianie przepływu serwera nie zapewnia ścisłą integrację w urządzeniach przenośnych i jest zalecane tylko na potrzeby weryfikacji koncepcji scenariuszy.

Przepływ klienta pozwala na lepszą integrację z funkcjami specyficznych dla urządzenia, takich jak logowanie jednokrotne, ponieważ opiera się na zestawów SDK dostarczanych przez dostawcę tożsamości.  Na przykład można zintegrować z zestawu SDK usługi Facebook Twojej aplikacji mobilnej.  Klienta mobilnego zamienia do aplikacji usługi Facebook i potwierdza usługi logowania jednokrotnego przed zamianą powrót do aplikacji mobilnej.

Cztery kroki są wymagane do włączenia uwierzytelniania w aplikacji:

* Rejestrowanie aplikacji do uwierzytelniania za pomocą dostawcy tożsamości.
* Konfigurowanie zaplecza usługi App Service.
* Uprawnienia tabeli dla uwierzytelnionych użytkowników tylko w wewnętrznej bazie danych usługi App Service.
* Dodawanie kodu uwierzytelniania do aplikacji.

Możesz ustawić uprawnienia w tabelach ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników. Identyfikator SID, uwierzytelnionego użytkownika umożliwia również modyfikowanie żądania.  Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy z uwierzytelnianiem] i dokumentacji Porada zestawu SDK serwera.

### <a name="caching"></a>Uwierzytelnianie: Przepływ serwera

Poniższy kod uruchamia proces serwera przepływ logowania przy użyciu dostawcy Google.  Dodatkowa konfiguracja jest wymagana ze względu na wymagania dotyczące zabezpieczeń dla dostawcy Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Ponadto Dodaj następującą metodę do klasy działania głównego:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

`GOOGLE_LOGIN_REQUEST_CODE` Zdefiniowane w głównego to działanie służy do `login()` metody i w ramach `onActivityResult()` metody.  Możesz wybrać dowolny unikatowy numer, tak długo, jak ten sam numer jest używany w ramach `login()` metody i `onActivityResult()` metody.  Jeśli do karty service (jak pokazano wcześniej) jest abstrakcyjny kod klienta, należy wywołać właściwe metody na karcie usługi.

Ponadto należy skonfigurować projekt dla customtabs.  Najpierw określić adresu URL przekierowania.  Dodaj poniższy fragment kodu do `AndroidManifest.xml`:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Dodaj **redirectUriScheme** do `build.gradle` pliku dla aplikacji:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Na koniec należy dodać `com.android.support:customtabs:28.0.0` listę zależności w `build.gradle` pliku:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Uzyskaj identyfikator zalogowanego użytkownika z **MobileServiceUser** przy użyciu **metodę getUserId** metody. Na przykład jak wywołanie asynchroniczne logowania interfejsów API za pomocą prognoz zobacz [Rozpoczynanie pracy z uwierzytelnianiem].

> [!WARNING]
> Schemat adresu URL, do wymienionej jest uwzględniana wielkość liter.  Upewnij się, że wszystkie wystąpienia `{url_scheme_of_you_app}` Uwzględnij wielkość liter.

### <a name="caching"></a>Tokeny uwierzytelniania pamięci podręcznej

Buforowanie tokenów uwierzytelniania należy do przechowywania nazwy użytkownika i token uwierzytelniania lokalnie na urządzeniu. Przy następnym uruchomieniu aplikacji, sprawdź pamięci podręcznej, a jeśli te wartości są obecne, możesz pominąć dziennika w ramach procedury i przywrócenia z magazynu trwałego klienta przy użyciu tych danych. Jednak te dane są poufne i powinny być przechowywane, szyfrowane pod kątem bezpieczeństwa, w przypadku, gdy telefon zostanie skradzione.  Możesz zobaczyć pełny przykład jak do pamięci podręcznej tokenów uwierzytelniania w [pamięci podręcznej w sekcji tokeny uwierzytelniania][7].

Podczas próby użycia tokenu wygasłe, pojawia się *401 Brak autoryzacji* odpowiedzi. Może obsługiwać błędy uwierzytelniania przy użyciu filtrów.  Filtry Przechwytywanie żądań z zapleczem usługi App Service. Kod filtru sprawdza odpowiedzi na 401, wyzwala procesu logowania, a następnie wznawia żądania, który wygenerował 401.

### <a name="refresh"></a>Używanie tokenów odświeżania

Czas życia zdefiniowanych jednej godziny jest tokenowi zwróconemu przez uwierzytelnianie usługi Azure App Service i autoryzacji.  Po upływie tego czasu ponownego uwierzytelnienia użytkownika.  Jeśli używasz długotrwałe token, który otrzymałeś za pośrednictwem uwierzytelniania przepływ klienta, a następnie ponownie można uwierzytelniać za pomocą uwierzytelnianie usługi Azure App Service i autoryzację przy użyciu tego samego tokenu.  Inny token w usłudze Azure App Service jest generowany nowy okres istnienia.

Można również zarejestrować dostawcę Aby używać tokenów odświeżania.  Token odświeżania nie jest zawsze dostępna.  Wymagana jest dodatkowa konfiguracja:

* Aby uzyskać **usługi Azure Active Directory**, skonfigurować klucz tajny klienta dla aplikacji Azure Active Directory.  Określ klucz tajny klienta w usłudze Azure App Service, podczas konfigurowania uwierzytelniania usługi Azure Active Directory.  Podczas wywoływania `.login()`, przekazać `response_type=code id_token` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Aby uzyskać **Google**, przekazać `access_type=offline` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Aby uzyskać **Account Microsoft**, wybierz opcję `wl.offline_access` zakresu.

Aby odświeżyć tokenu, należy wywołać `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Najlepszym rozwiązaniem należy utworzyć filtr, który wykrywa 401 odpowiedzi z serwera i podejmuje próbę odświeżenia tokenu użytkownika.

## <a name="log-in-with-client-flow-authentication"></a>Zaloguj się przy użyciu uwierzytelniania przepływu klienta

Ogólny proces zalogować się przy użyciu uwierzytelniania przepływu klienta jest następujący:

* Skonfiguruj uwierzytelnianie usługi Azure App Service i autoryzacji, tak jak serwer przepływu uwierzytelniania.
* Zintegruj dostawcy uwierzytelniania zestawu SDK do uwierzytelniania w celu wygenerowania tokenu dostępu.
* Wywołaj `.login()` metody w następujący sposób (`result` powinien być `AuthenticationResult`):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Zobacz pełny przykład kodu w następnej sekcji.

Zastąp `onSuccess()` metody za pomocą dowolnie kod mają być używane podczas logowania się pomyślnie.  `{provider}` Ciągu jest prawidłowy dostawca: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, lub **twitter**.  Jeśli zaimplementowano uwierzytelniania niestandardowego, można także zastosować tag dostawcy uwierzytelniania niestandardowego.

### <a name="adal"></a>Uwierzytelnianie użytkowników za pomocą Active Directory Authentication Library (ADAL)

Active Directory Authentication Library (ADAL) służy do logowania się użytkowników do aplikacji za pomocą usługi Azure Active Directory. Za pomocą identyfikatora logowania przepływ klienta jest często używane zamiast `loginAsync()` metody, ponieważ zapewnia bardziej natywnego działania środowiska użytkownika i umożliwia dodatkowych dostosowań.

1. Skonfiguruj zaplecza aplikacji mobilnej do logowania w usłudze AAD, wykonując [sposób konfigurowania usługi App Service dla nazwy logowania usługi Active Directory] [ 22] samouczka. Upewnij się ukończyć opcjonalny krok rejestrowanie natywnej aplikacji klienckiej.
2. Zainstaluj biblioteki ADAL, modyfikując z plikiem build.gradle, aby uwzględnić następujące definicje:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Dodaj następujący kod do aplikacji, co następujące elementy zastępcze:

    * Zastąp **INSERT-urzędu-tutaj** nazwą dzierżawy, w której aprowizowano aplikacji. Powinna być w formacie https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Zastąp **INSERT zasobów — identyfikator — tutaj** z Identyfikatorem klienta dla zaplecza aplikacji mobilnej. Można uzyskać identyfikator klienta z **zaawansowane** karcie **ustawienia usługi Azure Active Directory** w portalu.
    * Zastąp **INSERT klienta-ID — tutaj** z Identyfikatorem klienta został skopiowany z aplikacja kliencka macierzystego.
    * Zastąp **INSERT PRZEKIEROWANIA-URI-tutaj** z witryny */.auth/login/done* punktu końcowego, przy użyciu schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done* .

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Dostosuj komunikacji klient serwer

Połączenie klienta jest zwykle podstawowe połączenie HTTP przy użyciu podstawowej biblioteki HTTP dostarczony wraz z zestawu SDK systemu Android.  Istnieje kilka powodów dlaczego warto zmienić:

* Chcesz dopasować przekroczeń limitu czasu za pomocą alternatywnej biblioteki HTTP.
* Możesz podać pasek postępu.
* Chcesz dodać niestandardowy nagłówek do obsługi funkcji zarządzania interfejsu API.
* Chcesz przechwycić odpowiedzi o niepowodzeniu, dzięki czemu można zaimplementować ponowne uwierzytelnienie.
* Chcesz do rejestrowania żądań wewnętrznej bazy danych usługi analytics.

### <a name="using-an-alternate-http-library"></a>Za pomocą alternatywnej biblioteki HTTP

Wywołaj `.setAndroidHttpClientFactory()` metoda natychmiast po utworzeniu odwołania do klienta.  Na przykład, aby ustawić limit czasu połączenia do 60 sekund (zamiast domyślnego 10 sekund):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Zastosuj filtr postępu

Można zaimplementować intercept każde żądanie, implementując `ServiceFilter`.  Na przykład następujące aktualizacje pasek postępu wstępnie utworzone:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Ten filtr można dołączyć do klienta w następujący sposób:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Dostosowywanie nagłówków żądania

Należy użyć następującego `ServiceFilter` i dołączyć filtru w taki sam sposób jak `ProgressFilter`:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Skonfiguruj automatyczne serializacji

Można określić strategię konwersji, która ma zastosowanie do wszystkich kolumn przy użyciu [gson] [ 3] interfejsu API. Biblioteka kliencka systemu Android używa [gson] [ 3] w tle do wykonywania serializacji obiektów Java do formatu JSON, dane przed wysłaniem danych do usługi Azure App Service.  Poniższy kod używa **setFieldNamingStrategy()** metodę, aby ustawić strategii. W tym przykładzie spowoduje usunięcie początkowy znak ("m"), a następnie małą następny znak, dla każdej nazwy pola. Na przykład go czy przekształcając "mId" "id."  Wdrożenie strategii konwersji, aby zmniejszyć zapotrzebowanie na `SerializedName()` adnotacji dla większości pól.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Ten kod musi zostać wykonana przed utworzeniem odwołania klienta mobilnego za pomocą **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Rozpoczynanie pracy z uwierzytelnianiem]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
