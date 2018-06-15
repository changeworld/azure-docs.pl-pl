---
title: Jak używać zestawu Azure Mobile Apps SDK dla systemu Android | Dokumentacja firmy Microsoft
description: Jak używać zestawu Azure Mobile Apps SDK dla systemu Android
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
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: d89aa308ab8f6684cebbec49bbefdcb54d77c886
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869720"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak używać zestawu Azure Mobile Apps SDK dla systemu Android

Ten przewodnik przedstawia sposób użycia klienta dla systemu Android SDK for Mobile Apps do zaimplementowania typowych scenariuszy, takich jak:

* Wyszukiwanie danych (wstawiania, aktualizowania i usuwania).
* Uwierzytelnianie.
* Obsługa błędów.
* Dostosowywanie klienta.

Ten przewodnik koncentruje się na zestaw SDK systemu Android po stronie klienta.  Aby uzyskać więcej informacji na temat zestawów SDK po stronie serwera dla Mobile Apps, zobacz [pracować z zaplecza .NET SDK] [ 10] lub [sposób użycia zaplecza Node.js SDK][11].

## <a name="reference-documentation"></a>Dokumentacji

Można znaleźć [dokumentacja interfejsu API Javadocs] [ 12] biblioteki klienta dla systemu Android w witrynie GitHub.

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK aplikacji mobilnych Azure dla systemu Android obsługuje poziomy interfejsu API 19 do 24 (KitKat za pośrednictwem nugacie) Telefon i tablet rozmiarach.  Uwierzytelnianie, w szczególności korzysta z typowym podejściem framework sieci web do zbierania poświadczeń.  Uwierzytelnianie serwera przepływu nie działa z niewielkich współczynnik urządzeń, takich jak obserwowanie.

## <a name="setup-and-prerequisites"></a>Instalacji i wymagania wstępne

Zakończenie [szybkiego startu Mobile Apps](app-service-mobile-android-get-started.md) samouczka.  To zadanie gwarantuje, że zostały spełnione wszystkie wymagania wstępne związane z opracowywaniem Azure Mobile Apps.  Procedury szybkiego startu pomaga również skonfigurować konta i utworzyć pierwszy zaplecza aplikacji mobilnej.

Jeśli nie chcesz Ukończ samouczek Szybki Start, należy wykonać następujące zadania:

* [Tworzenie zaplecza aplikacji mobilnej] [ 13] do korzystania z aplikacji systemu Android.
* W programie Android Studio [aktualizacji Gradle kompilacja plików](#gradle-build).
* [Włącz uprawnień internetowych](#enable-internet).

### <a name="gradle-build"></a>Zaktualizuj plik kompilacji narzędzia Gradle

Zmiana obu **build.gradle** plików:

1. Dodaj ten kod do *projektu* poziom **build.gradle** pliku wewnątrz *buildscript* tagu:

    ```text
    buildscript {
        repositories {
            jcenter()
        }
    }
    ```

2. Dodaj ten kod do *aplikacji modułu* poziom **build.gradle** pliku wewnątrz *zależności* tagu:

    ```text
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Najnowsza wersja jest obecnie 3.4.0. Obsługiwane wersje są wyświetlane [w serwisie bintray][14].

### <a name="enable-internet"></a>Włącz uprawnień internetowych

Aby uzyskać dostęp do platformy Azure, aplikacji musi mieć włączone uprawnień internetowych. Jeśli nie zostało jeszcze jest włączone, Dodaj następujący wiersz kodu do Twojej **AndroidManifest.xml** pliku:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Utwórz połączenie klienta

Azure Mobile Apps udostępnia cztery funkcje dla twojej aplikacji mobilnej:

* Dostęp do danych i synchronizacji z usługą Azure Mobile Services aplikacje w trybie Offline.
* Wywoływania niestandardowych interfejsów API napisany za pomocą zestawu SDK usługi Azure Mobile Apps serwera.
* Uwierzytelnianie za pomocą usługi aplikacji Azure uwierzytelniania i autoryzacji.
* Rejestracja powiadomień z koncentratorami powiadomień wypychanych.

Każda z tych funkcji wymaga najpierw utworzenia `MobileServiceClient` obiektu.  Tylko jeden `MobileServiceClient` obiekt powinien zostać utworzony w ramach sieci klientów urządzeń przenośnych (to znaczy, należy go wzorzec Singleton).  Aby utworzyć `MobileServiceClient` obiektu:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` Jest ciągiem lub obiekt adres URL, który wskazuje z zaplecza aplikacji mobilnych.  Korzystania z usługi aplikacji Azure do hostowania zaplecza przenośnych upewnij się, możesz użyć bezpiecznego `https://` wersję adresu URL.

Klient wymaga również dostęp do działania lub kontekstu - `this` parametru w przykładzie.  Konstrukcja MobileServiceClient powinno się zdarzyć w `onCreate()` odwołuje do metody działania `AndroidManifest.xml` pliku.

Jako najlepsze rozwiązanie należy abstrakcyjnej komunikacji z serwerem do własnej klasy (wzorca singleton).  W takim przypadku należy przekazać działaniem określonym w Konstruktorze odpowiednio skonfigurować usługę.  Na przykład:

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

Można teraz wywołać `AzureServiceAdapter.Initialize(this);` w `onCreate()` metody działania głównego.  Inne metody potrzeby uzyskiwania dostępu do użycia klienta `AzureServiceAdapter.getInstance();` uzyskać odwołania do karty usługi.

## <a name="data-operations"></a>Operacje na danych

Podstawowy zestaw SDK usługi Azure Mobile Apps jest zapewnienie dostępu do danych przechowywanych w ramach usług SQL Azure na zaplecza aplikacji mobilnej.  Można uzyskać dostępu do te dane przy użyciu klasy jednoznacznie (preferowane) lub wykonywania kwerend (niezalecane).  Zbiorczego ta sekcja dotyczy przy użyciu silnie typizowanej klasy.

### <a name="define-client-data-classes"></a>Definiowanie klas danych klienta

Dostęp do danych z tabel SQL Azure, definiowania klas danych klienta, które odpowiadają z tabelami w zaplecza aplikacji mobilnej. Przykłady w tym temacie założono tabela o nazwie **MyDataTable**, która zawiera następujące kolumny:

* id
* Tekst
* Zakończenie

Odpowiedni obiekt typu po stronie klienta znajduje się w pliku o nazwie **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Dodaj metody pobierającej i ustawiającej dla każdego pola, które można dodać.  Jeśli tabela SQL Azure zawiera więcej kolumn, należy dodać odpowiednie pola do tej klasy.  Na przykład jeśli DTO kolumna priorytet liczb całkowitych miał (obiektu transferu danych), a następnie można dodać tego pola, wraz z jego metody pobierającej i ustawiającej:

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

Aby dowiedzieć się, jak utworzyć dodatkowe tabele w zapleczu swojej Mobile Apps, zobacz [porady: Definiowanie kontrolera tabeli] [ 15] (zaplecze .NET) lub [zdefiniuj tabel za pomocą dynamicznej schematu] [ 16] (zaplecza Node.js).

Azure Mobile Apps tabeli wewnętrznej bazy danych definiuje pięć specjalne pól, z których cztery są dostępne dla klientów:

* `String id`: Globalnie unikatowy identyfikator dla rekordu.  Najlepszym rozwiązaniem, wprowadź identyfikator reprezentację ciągu [UUID] [ 17] obiektu.
* `DateTimeOffset updatedAt`: Data/godzina ostatniej aktualizacji.  Pole updatedAt jest ustawiana przez serwer i nie powinno być używane przez kod klienta.
* `DateTimeOffset createdAt`: Data/godzina utworzenia obiektu.  Pole createdAt jest ustawiana przez serwer i nie powinno być używane przez kod klienta.
* `byte[] version`: Zazwyczaj reprezentowany jako ciąg, wersja również jest ustawiana przez serwer.
* `boolean deleted`: Wskazuje, że rekord została usunięta, ale nie jest jeszcze przeczyszczane.  Nie używaj `deleted` jako właściwość w klasie.

`id` Pole jest wymagane.  `updatedAt` Pola i `version` pola są używane do synchronizacji w trybie offline (przyrostowej synchronizacji i wystąpi konflikt rozpoznawania odpowiednio).  `createdAt` Pole jest polem Odwołanie i nie jest używana przez klienta.  Nazwy "w locie" nazw właściwości i nie są zmieniane.  Można jednak utworzyć mapowanie między obiektu i nazwy "w locie" przy użyciu [gson] [ 3] biblioteki.  Na przykład:

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

Do uzyskania dostępu do tabeli, należy najpierw utworzyć [MobileServiceTable] [ 8] obiektu przez wywołanie metody **getTable** metoda [MobileServiceClient][9].  Ta metoda ma dwa przeciążenia:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

W poniższym kodzie **mClient** jest odwołaniem do obiektu MobileServiceClient.  Pierwszy przeciążenia jest używana, gdzie nazwa klasy i nazwy tabeli są takie same i jest co najmniej jedna używana z opcją szybkiego startu:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Przeciążenie drugi jest używany podczas Nazwa tabeli jest inna niż nazwa klasy: Nazwa tabeli jest pierwszym parametrem.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Zapytania dotyczącego tabeli wewnętrznej bazy danych

Najpierw należy uzyskać odwołania do tabeli.  Następnie można wykonać zapytania na odwołania do tabeli.  Zapytanie jest dowolną kombinację:

* A `.where()` [klauzuli filtru](#filtering).
* `.orderBy()` [Porządkowanie klauzuli](#sorting).
* A `.select()` [pola wyboru klauzuli](#selection).
* A `.skip()` i `.top()` dla [stronicowanej wyniki](#paging).

Klauzule musi być podana w wyżej wymieniona kolejność.

### <a name="filter"></a> Filtrowanie wyników

Formularz ogólny kwerendy jest:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Poprzedni przykład zwraca wszystkie wyniki (maksymalnie maksymalny rozmiar strony ustawiony przez serwer).  `.execute()` Metoda wykonuje zapytanie do wewnętrznej bazy danych.  Zapytanie jest konwertowana na [OData v3] [ 19] zapytania przed ich przesłaniem do zaplecza aplikacji mobilnej.  Po otrzymaniu zaplecza aplikacji mobilnej konwertuje zapytania do instrukcji SQL przed jej wykonanie w wystąpieniu programu SQL Azure.  Ponieważ sieci działania dopiero po pewnym czasie `.execute()` metoda zwraca [ `ListenableFuture<E>` ] [ 18].

### <a name="filtering"></a>Filtr zwrócił danych

Następujące wykonywania zapytania zwraca wszystkie elementy z **ToDoItem** tabeli where **pełną** jest równe **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** jest odwołanie do tabeli usługi mobilnej, który utworzono wcześniej.

Zdefiniuj filtr przy użyciu **gdzie** wywołanie metody dla odwołania do tabeli. **Gdzie** następuje metody **pola** metody następuje metodę, która określa logicznej predykatu. Predykatu metod uwzględnić **eq** (równe), **ne** (nie równa), **gt** (większe niż) **ge** (większe lub równe), **lt** (poniżej), **le** (mniejsze niż lub równe). Te metody umożliwiają porównanie pola numer i ciąg do określonej wartości.

Można filtrować według daty. Następujące metody umożliwiają porównanie pola Data całego lub części daty: **roku**, **miesiąca**, **dzień**, **godzina**, **minutę**, i **drugi**. Poniższy przykład umożliwia dodanie filtru dla elementów których *termin* jest równe 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Następujące metody obsługi złożonych filtrów w polach ciąg: **startsWith**, **endsWith**, **concat**, **podciąg**, **indexOf**, **Zastąp**, **toLower**, **toUpper**, **trim**, i **długość**. Następujące przykładowe filtry w tabeli wiersze, w których *tekst* kolumny rozpoczyna się od "PRI0."

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Następujące metody operator są obsługiwane na pola numerów: **dodać**, **sub**, **mul**, **div**, **mod**, **floor**, **limitu**, i **zaokrąglona**. Następujące przykładowe filtry w tabeli wiersze, w których **czas trwania** jest liczbą parzystą.

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

Operatory logiczne grupy i gniazda:

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

Aby uzyskać bardziej szczegółowe omówienie i przykłady filtrowania, zobacz [eksploracji formatem model zapytań klienta dla systemu Android][20].

### <a name="sorting"></a>Sortowanie zwrócone dane

Poniższy kod zwraca wszystkie elementy z tabeli z **ToDoItems** sortowane w kolejności rosnącej przez *tekst* pola. *mToDoTable* jest odwołanie do utworzonego wcześniej tabeli wewnętrznej bazy danych:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Pierwszy parametr **orderBy** metoda jest taka sama jak nazwa pola, w których mają być sortowane ciąg. Drugi parametr używa **QueryOrder** wyliczeniu, aby określić, czy mają być sortowane w kolejności rosnącej lub malejącej.  Jeśli filtrowania przy użyciu ***gdzie*** metody ***gdzie*** metody należy wywołać przed ***orderBy*** — metoda.

### <a name="selection"></a>Wybrać określone kolumny

Poniższy kod przedstawia sposób zwrócenia wszystkich elementów z tabeli z **ToDoItems**, ale wyświetlane są tylko **pełną** i **tekst** pola. **mToDoTable** jest odwołanie do tabeli wewnętrznej bazy danych utworzonej wcześniej.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry funkcji wybierz są ciąg nazwy kolumn w tabeli, które chcesz przywrócić.  **Wybierz** metody musi wykonać metody, takie jak **gdzie** i **orderBy**. Może występować przez metody stronicowania, takie jak **pominąć** i **górnej**.

### <a name="paging"></a>Zwróć dane strony

Dane są **zawsze** zwracane w stronach.  Maksymalną liczbę rekordów zwróconych jest ustawiana przez serwer.  Jeśli klient żąda więcej rekordów, serwer zwraca maksymalną liczbę rekordów.  Domyślnie maksymalny rozmiar strony na serwerze jest 50 rekordów.

Pierwszym przykładzie pokazano, jak wybrać pierwsze pięć elementy z tabeli. Zwraca elementy z tabeli **ToDoItems**. **mToDoTable** jest odwołanie do utworzonego wcześniej tabeli wewnętrznej bazy danych:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Oto zapytanie, które pomija pierwszych pięciu elementów, a następnie zwraca następnych pięciu:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Jeśli chcesz pobrać wszystkie rekordy w tabeli, zaimplementuj kod, aby przejść przez wszystkie strony:

```java
List<MyDataModel> results = new List<MyDataModel>();
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

Żądanie dla wszystkich rekordów za pomocą tej metody tworzy co najmniej dwa żądania do zaplecza aplikacji mobilnej.

> [!TIP]
> Wybieranie rozmiaru prawej strony jest kompromis między użycia pamięci podczas żądania jest wykonywane, przepustowości i opóźnienia w całkowicie odbiera dane.  Wartość domyślna (50 rekordów) jest odpowiednia dla wszystkich urządzeń.  Wyłącznie w przypadku obsługi większych urządzeń pamięci, należy zwiększyć do 500.  Znaleźliśmy że zwiększenie rozmiaru strony ponad 500 rejestruje wyniki nie do przyjęcia opóźnienia i problemy z dużej ilości pamięci.

### <a name="chaining"></a>Porady: łączenie metody zapytania

Metody stosowane badania tabel zaplecza może zostać dołączona. Łańcucha metody zapytań można wybrać określone kolumny filtrowane wierszy, które są sortowane i stronicowana. Można tworzyć złożone filtry logiczne.  Każda metoda zapytanie zwraca obiekt zapytania. Aby zakończyć szereg metod i faktycznie uruchom zapytanie, należy wywołać **wykonania** metody. Na przykład:

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

Metody zapytań łańcuchowa muszą być uporządkowane w następujący sposób:

1. Filtrowanie (**gdzie**) metod.
2. Sortowanie (**orderBy**) metod.
3. Wybór (**wybierz**) metod.
4. stronicowanie (**pominąć** i **górnej**) metod.

## <a name="binding"></a>Wiązanie danych do interfejsu użytkownika

Powiązanie danych obejmuje trzy składniki:

* Źródło danych
* Układ ekranu
* Karta, który wiąże ze sobą dwa.

W naszym przykładowym kodzie zwróconych danych z tabeli Mobile Apps SQL Azure **ToDoItem** do tablicy. To działanie jest wspólnego wzorca dla danych aplikacji.  Zapytania bazy danych często zwracać kolekcji wierszy, do których klient pobiera listy lub tablicy. W tym przykładzie tablicy jest źródłem danych.  Kod określa układ ekranu, który definiuje widoku danych, który pojawia się na urządzeniu.  Dwa powiązanych wraz z karty, którego ten kod jest rozszerzeniem z **ArrayAdapter&lt;ToDoItem&gt;**  klasy.

#### <a name="layout"></a>Definiowania układu

Układ jest definiowany przez kilka fragmentów kodu XML. Biorąc pod uwagę istniejącego układu, poniższy kod przedstawia **ListView** chcemy wypełnić z naszych danych serwera.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

W powyższym kodzie *listitem* atrybut określa identyfikator układu dla pojedynczego wiersza na liście. Ten kod pobiera utworzyć raz dla każdego elementu na liście i określa pole wyboru i jego tekst. Nie są wyświetlane w tym układzie **identyfikator** pola i bardziej złożonej układu określić dodatkowe pola na ekranie. Ten kod jest **row_list_to_do.xml** pliku.

```java
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
Ponieważ naszych widoku źródła danych jest tablicą **ToDoItem**, możemy podklasy naszych karty z **ArrayAdapter&lt;ToDoItem&gt;**  klasy. To podklasa tworzy widok dla każdego **ToDoItem** przy użyciu **row_list_to_do** układu.  W naszym kodzie definiujemy następujące klasy, która jest rozszerzeniem **ArrayAdapter&lt;E&gt;**  klasy:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Zastąpienie kart **getView** metody. Na przykład:

```
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

Tworzymy wystąpienie tej klasy w naszych działań w następujący sposób:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Drugi parametr dla konstruktora ToDoItemAdapter jest odwołanie do układu. Firma Microsoft można teraz utworzyć wystąpienie **ListView** i przypisz do karty **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Użyj karty do tworzenia powiązań interfejsu użytkownika

Teraz można przystąpić do używania wiązania z danymi. Poniższy kod pokazuje, jak można pobrać elementów w tabeli i wypełnia zwracane elementy lokalne karty.

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

Wywołaj karty żadnych modyfikacji **ToDoItem** tabeli. Ponieważ zmiany są wykonywane na podstawie rekordu na podstawie, można obsługiwać pojedynczy wiersz zamiast kolekcji. Po wstawieniu elementu wywołać **dodać** metody na karcie; podczas usuwania, wywołaj **Usuń** — metoda.

Znajduje się pełny przykład w [projektu szybkiego startu dla systemu Android][21].

## <a name="inserting"></a>Wstawianie danych do wewnętrznej bazy danych

Tworzy wystąpienie *ToDoItem* klasy i ustawienia swoich właściwości.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Następnie użyj **operacji insert()** do wstawienia obiektu:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Dopasowań zwróconą jednostkę danych wstawione do tabeli wewnętrznej bazy danych zawiera identyfikator i inne wartości (takie jak `createdAt`, `updatedAt`, i `version` pól) Ustaw do wewnętrznej bazy danych.

Tabele Mobile Apps, wymagają kolumna klucza podstawowego o nazwie **identyfikator**. Ta kolumna musi być ciągiem. Domyślna wartość w kolumnie identyfikator jest identyfikatorem GUID.  Możesz podać inne unikatowe wartości, takie jak adresy e-mail lub nazwy użytkowników. Jeśli nie podano wartość Identyfikatora ciągu wstawionego rekordu, wewnętrznej bazy danych generuje nowy identyfikator GUID.

Identyfikator ciągami zapewniają następujące korzyści:

* Identyfikatory mogą być generowane bez wprowadzania podróż do bazy danych.
* Rekordy są łatwiejsze do scalenia z różnych tabel lub baz danych.
* Wartości Identyfikatora lepszą integrację z logiki aplikacji.

Ciąg Identyfikatora wartości są **REQUIRED** obsługę synchronizacji w trybie offline.  Nie można zmienić identyfikatora, gdy jest on przechowywany w bazie danych zaplecza.

## <a name="updating"></a>Aktualizuj dane w aplikacji mobilnej

Aby zaktualizować dane w tabeli, należy przekazać nowy obiekt do **update()** metody.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

W tym przykładzie *elementu* jest odwołanie do wiersza w *ToDoItem* tabeli, która wystąpiła niektóre zmiany wprowadzone do niego.  Wiersz o takim samym **identyfikator** jest aktualizowany.

## <a name="deleting"></a>Usuwanie danych w aplikacji mobilnej

Poniższy kod przedstawia sposób usuwania danych z tabeli, określając obiektu danych.

```java
mToDoTable
    .delete(item);
```

Można również usunąć element, określając **identyfikator** pole wiersza do usunięcia.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Wyszukiwanie określonego elementu według identyfikatora

Wyszukiwanie elementu z określonym **identyfikator** pole z **lookUp()** metody:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Porady: Praca z danymi bez typu

Nieuwzględniające typów modelu programowania zapewnia dokładną kontrolę nad serializacji JSON.  Brak niektórych typowych scenariuszy, w których warto użyć bez typu modelu programowania. Na przykład, jeśli tabela wewnętrznej bazy danych zawiera wiele kolumn, należy odwoływać podzestaw kolumn.  Model typu wymaga zdefiniowania wszystkich kolumn, które są zdefiniowane w zaplecza aplikacji mobilnej w klasie danych.  Większość wywołań interfejsu API w celu uzyskania dostępu do danych są podobne do typu wywołania programowania. Główną różnicą jest to, czy w modelu bez typu wywołuje metody na **MobileServiceJsonTable** obiektu, a nie **MobileServiceTable** obiektu.

### <a name="json_instance"></a>Utwórz wystąpienie tabeli bez typu

Podobnie jak typu modelu, możesz uruchomić pobierania odwołania do tabeli, ale w takim przypadku jest **MobileServicesJsonTable** obiektu. Uzyskaj odwołanie wywołując **getTable** metody w wystąpieniu klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po utworzeniu wystąpienia **MobileServiceJsonTable**, praktycznie ma tego samego interfejsu API dostępne jako typu modelu programowania. W niektórych przypadkach metody zająć bez typu parametru zamiast parametru typu.

### <a name="json_insert"></a>Wstaw do tabeli bez typu
Poniższy kod przedstawia sposób wykonywania instrukcji insert. Pierwszym krokiem jest utworzenie [JsonObject][1], który jest częścią [gson] [ 3] biblioteki.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Następnie należy użyć **operacji insert()** można wstawić nieuwzględniające typów obiektów do tabeli.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Aby uzyskać identyfikator obiektu wstawionego, należy użyć **getAsJsonPrimitive()** metody.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Usuń z tabeli bez typu
Poniższy kod przedstawia sposób usuwania wystąpienia, w tym przypadku to samo wystąpienie elementu **JsonObject** utworzony wcześniej *Wstaw* przykład. Kod jest taki sam, podobnie jak w przypadku typu wielkość liter, ale metoda ma inny podpis, ponieważ odwołuje się on **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Możesz także usunąć wystąpienia bezpośrednio za pomocą jego Identyfikatora:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Zwracanie wszystkich wierszy z tabeli bez typu
Poniższy kod przedstawia sposób pobierania całej tabeli. Ponieważ używasz tabeli JSON można selektywnie można pobrać niektórych kolumn tabeli.

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

Ten sam zestaw filtrowania filtrowania i stronicowania metod, które są dostępne dla typu modelu są dostępne dla modelu bez typu.

## <a name="offline-sync"></a>Implementowanie synchronizacji w trybie Offline

Zestaw SDK usługi Azure Mobile Apps klienta implementuje również synchronizacji danych w trybie offline przy użyciu bazy danych SQLite do przechowywania kopii danych serwera lokalnie.  Operacje wykonywane w trybie offline tabeli nie jest wymagana łączność do pracy.  Synchronizacja w trybie offline pomocy wydajność kosztem bardziej złożonej logice do rozwiązywania konfliktów i odporność.  Zestaw SDK usługi Azure Mobile Apps klient wykonuje następujące funkcje:

* Synchronizacja przyrostowa: Tylko zaktualizowanych i nowych rekordów są pobierane, zapisywanie zużycie przepustowości i pamięci.
* Optymistycznej współbieżności: Operacje są uznawane za powiodło się.  Rozwiązywanie konfliktów jest odłożona do aktualizacji są wykonywane na serwerze.
* Rozwiązywanie konfliktów: Zestaw SDK wykrywa przypadku powodujące konflikt zmiany zostały wprowadzone na serwerze oraz udostępnia punkty zaczepienia, aby ostrzec użytkownika.
* Usuwania nietrwałego: Usunięte rekordy są oznaczane usuniętych, dzięki czemu inne urządzenia, aby zaktualizować pamięci podręcznej w trybie offline.

### <a name="initialize-offline-sync"></a>Inicjowanie synchronizacji w trybie Offline

Każda tabela w trybie offline, musi być zdefiniowana w pamięci podręcznej w trybie offline przed użyciem.  Zwykle definicji tabeli odbywa się natychmiast po utworzeniu klienta:

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

Dla tabeli online, możesz użyć `.getTable()`.  Do tabeli w trybie offline, należy użyć `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Wszystkie metody, które są dostępne dla online tabel (w tym filtrowania, sortowania, stronicowania, wstawiania danych, aktualizowanie danych i usunięcie danych) działa równie dobrze w tabelach w trybie online i jest w trybie offline.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizowanie lokalnej pamięci podręcznej w trybie Offline

Synchronizacja jest w formancie aplikacji.  Oto przykład metody synchronizacji:

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

Jeśli podano nazwę kwerendy do `.pull(query, queryname)` metody, a następnie synchronizacja przyrostowa służy do zwracania, tylko rekordy, które zostały utworzone lub zmienione od ostatniego pomyślnie ukończono ściągania.

### <a name="handle-conflicts-during-offline-synchronization"></a>Obsługa konflikty podczas synchronizacji w trybie Offline

Jeśli wystąpi konflikt podczas `.push()` operacji `MobileServiceConflictException` jest generowany.   Element wystawiony serwera jest osadzony w wyjątek i mogą zostać pobrane przez `.getItem()` na wyjątek.  Dostosuj naciśnięcie przez wywołanie obiektu MobileServiceSyncContext następujące elementy:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Gdy wszystkie konflikty zostały oznaczone jako mają, wywołaj `.push()` ponownie, aby rozwiązać wszystkie konflikty.

## <a name="custom-api"></a>Wywołaj niestandardowego interfejsu API

Niestandardowy interfejs API umożliwia zdefiniowanie niestandardowe punkty końcowe, które udostępniają funkcje serwera które nie mapowania insert, update, usuwania lub operacja odczytu. Przy użyciu niestandardowego interfejsu API, może mieć większą kontrolę nad wiadomości, w tym odczytywanie ustawienie nagłówki komunikatów HTTP i Definiowanie formatu treści wiadomości innych niż JSON.

Na kliencie z systemem Android należy wywołać **invokeApi** metodę do wywołania niestandardowych punkt końcowy interfejsu API. Poniższy przykład przedstawia sposób wywołania interfejsu API punktu końcowego o nazwie **completeAll**, który zwraca klasę kolekcji o nazwie **MarkAllResult**.

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

**InvokeApi** metoda jest wywoływana po stronie klienta, który wysyła żądanie POST do nowego niestandardowego interfejsu API. Wynik zwracany przez niestandardowy interfejs API wyświetleniem w oknie dialogowym komunikatu, ponieważ nie ma żadnych błędów. Inne wersje **invokeApi** można opcjonalnie Wysyłanie obiektu w treści żądania, określ metodę HTTP i Wyślij parametry zapytania w żądaniu. Bez typu wersje **invokeApi** znajdują się również.

## <a name="authentication"></a>Dodawanie uwierzytelniania do aplikacji

Samouczki przedstawiają szczegółowo już sposób dodania tych funkcji.

Usługa aplikacji obsługuje [uwierzytelnianie użytkowników aplikacji](app-service-mobile-android-get-started-users.md) przy użyciu różnych dostawców tożsamości zewnętrznych: Facebook, Google, Microsoft Account, Twitter i Azure Active Directory. Tabele, aby ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników można ustawić uprawnienia. Umożliwia także tożsamość uwierzytelnionych użytkowników do zaimplementowania reguły autoryzacji w sieci wewnętrznej bazy danych.

Obsługiwane są dwa przepływy uwierzytelniania: **serwera** przepływu i **klienta** przepływu. Przepływ server zapewnia najprostszą środowisko uwierzytelniania, zależy od interfejsu sieci web dostawcy tożsamości.  Nie dodatkowe zestawy SDK są wymagane do wdrożenia serwera przepływ uwierzytelniania. Uwierzytelnianie serwera przepływu nie głęboką integrację do urządzeń przenośnych i jest zalecane tylko dla dowód scenariuszy koncepcji.

Przepływ klienta umożliwia lepszą integrację z możliwości specyficznych dla urządzeń, takich jak logowanie jednokrotne opiera się na zestawy SDK udostępniane przez dostawcę tożsamości.  Na przykład można zintegrować z zestawem SDK usługi Facebook aplikacji mobilnej.  Klientów urządzeń przenośnych zamienia do aplikacji usługi Facebook i sprawdza z logowania jednokrotnego przed wymiany aplikacji mobilnej.

Cztery kroki są wymagane do włączenia uwierzytelniania w aplikacji:

* Rejestrowanie aplikacji do uwierzytelniania przy użyciu dostawcy tożsamości.
* Skonfiguruj z wewnętrzną bazą danych z usługi aplikacji.
* Uprawnienia tabeli uwierzytelnionych użytkowników tylko do wewnętrznej bazy danych z usługi aplikacji.
* Dodaj kod uwierzytelniania do aplikacji.

Tabele, aby ograniczyć dostęp dla określonych operacji tylko do uwierzytelnionych użytkowników można ustawić uprawnienia. Identyfikator SID użytkownika uwierzytelnionego umożliwia również modyfikowanie żądania.  Aby uzyskać więcej informacji, przejrzyj [Rozpoczynanie pracy z uwierzytelnianiem] i dokumentacji Porada zestawu SDK serwera.

### <a name="caching"></a>Uwierzytelniania: Przepływ serwera

Następujący kod uruchamia proces logowania przepływu serwera przy użyciu dostawcy usługi Google.  Ze względu na wymagania zabezpieczeń dla dostawcy Google jest wymagana dodatkowa konfiguracja:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Ponadto Dodaj następującą metodę do klasy głównym działania:

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

`GOOGLE_LOGIN_REQUEST_CODE` Zdefiniowane w głównym Twojego to działanie służy do `login()` — metoda i poziomu `onActivityResult()` metody.  Można wybrać dowolny unikatowy numer, tak długo, jak ten sam numer jest używany w ramach `login()` — metoda i `onActivityResult()` metody.  Jeśli kod klienta jest abstrakcyjny karty service (jak pokazano wcześniej), należy wywołać odpowiednie metody na karcie usługi.

Należy również skonfigurować projekt dla customtabs.  Najpierw określ URL przekierowania.  Dodaj poniższy fragment do `AndroidManifest.xml`:

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

Dodaj **redirectUriScheme** do `build.gradle` plików dla aplikacji:

```text
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

Na koniec należy dodać `com.android.support:customtabs:23.0.1` listę zależności w `build.gradle` pliku:

```text
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.google.code.gson:gson:2.3'
    compile 'com.google.guava:guava:18.0'
    compile 'com.android.support:customtabs:23.0.1'
    compile 'com.squareup.okhttp:okhttp:2.5.0'
    compile 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Uzyskaj identyfikator zalogowanego użytkownika z **MobileServiceUser** przy użyciu **metodę getUserId** metody. Na przykład sposobu użycia prognoz do wywołania asynchronicznego logowania interfejsów API, zobacz [Rozpoczynanie pracy z uwierzytelnianiem].

> [!WARNING]
> Schemat adresu URL wymieniony jest rozróżniana wielkość liter.  Upewnij się, że wszystkie wystąpienia `{url_scheme_of_you_app}` wielkość liter.

### <a name="caching"></a>Tokeny uwierzytelniania pamięci podręcznej

Buforowanie tokeny uwierzytelniania wymaga do przechowywania nazwy użytkownika i token uwierzytelniania lokalnie na urządzeniu. Przy następnym uruchomieniu aplikacji, sprawdź pamięci podręcznej, a jeśli te wartości są obecne, możesz pominąć dziennika w procedurze i rehydrate klienta przy użyciu tych danych. Jednak te dane są poufne i powinny być przechowywane, szyfrowane ze względów bezpieczeństwa w przypadku kradzieży telefonu.  Widać pełny przykład jak do pamięci podręcznej uwierzytelniania tokenów w [pamięci podręcznej sekcji tokeny uwierzytelniania][7].

Podczas próby użycia wygasły token, pojawi się *401 nieautoryzowane* odpowiedzi. Może obsługiwać błędy uwierzytelniania za pomocą filtrów.  Filtry przechwycenia żądań aplikacji usługi wewnętrznej bazy danych. Kod filtru testów odpowiedzi na 401, proces logowania i zostanie wznowiony żądania, który wygenerował kod 401.

### <a name="refresh"></a>Używaj tokenów odświeżania

Token zwracany przez usługi Azure App Service uwierzytelniania i autoryzacji ma zdefiniowany czas jedną godzinę.  Po tym okresie ponownego uwierzytelnienia użytkownika.  Jeśli używasz długotrwałe token, który ma odebranych za pośrednictwem uwierzytelniania przepływu klienta, a następnie ponownie można uwierzytelniać z uwierzytelnianiem usługi aplikacji Azure i autoryzację za pomocą tego samego tokenu.  Inny token usługi Azure App Service jest generowany nowy okres istnienia.

Można również zarejestrować dostawcę, który ma być używany tokenów odświeżania.  Token odświeżania nie zawsze jest dostępne.  Wymagana jest dodatkowa konfiguracja:

* Aby uzyskać **usługi Azure Active Directory**, skonfiguruj klucz tajny klienta aplikacji Azure Active Directory.  Określ klucz tajny klienta w usłudze Azure App Service, podczas konfigurowania uwierzytelniania usługi Azure Active Directory.  Podczas wywoływania metody `.login()`, Przekaż `response_type=code id_token` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Aby uzyskać **Google**, Przekaż `access_type=offline` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Aby uzyskać **Account Microsoft**, wybierz pozycję `wl.offline_access` zakresu.

Aby odświeżyć token, należy wywołać `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Najlepszym rozwiązaniem należy utworzyć filtr wykrywa 401 odpowiedzi z serwera i podejmuje próbę odświeżenia tokenu użytkownika.

## <a name="log-in-with-client-flow-authentication"></a>Zaloguj się przy użyciu uwierzytelniania przepływu klienta

Ogólny proces logowania się za pomocą klienta przepływ uwierzytelniania wygląda następująco:

* Konfigurowanie usługi Azure App Service uwierzytelniania i autoryzacji, jak w przypadku uwierzytelniania serwera przepływu.
* Integracja dostawcy uwierzytelniania zestawu SDK dla uwierzytelniania, aby wygenerować token dostępu.
* Wywołanie `.login()` metody w następujący sposób:

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

Zastąp `onSuccess()` metody z dowolnym kod chcesz użyć na pomyślnego logowania.  `{provider}` Ciąg jest prawidłowym dostawcą: **aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**, lub **twitter**.  Jeśli zaimplementowano niestandardowe uwierzytelnianie, można również zastosować tag dostawcy uwierzytelniania niestandardowego.

### <a name="adal"></a>Uwierzytelnianie użytkowników z Active Directory Authentication Library (ADAL)

Active Directory Authentication Library (ADAL) służy do logowania się użytkowników do aplikacji za pomocą usługi Azure Active Directory. Przy użyciu identyfikatora logowania przepływu klienta jest często używane zamiast `loginAsync()` metody, ponieważ zawiera więcej natywnego działania środowiska użytkownika i zezwala na dodatkowe dostosowanie.

1. Konfigurowanie zaplecza aplikacji mobilnej dla logowania w usłudze AAD, wykonując [konfigurowania aplikacji usługi logowania usługi Active Directory] [ 22] samouczka. Upewnij się ukończyć opcjonalny krok rejestrowania aplikację native client.
2. Zainstaluj biblioteki ADAL, modyfikując plik build.gradle, aby uwzględnić następujące definicje:

```
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
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. Dodaj następujący kod do aplikacji, co następujące elementy zastępcze:

* Zastąp **INSERT urzędu tutaj** o nazwie dzierżawy, w którym są udostępniane aplikacji. Format powinien być https://login.microsoftonline.com/contoso.onmicrosoft.com.
* Zastąp **Wstaw zasób — identyfikator-tutaj** z Identyfikatorem klienta dla zaplecza aplikacji mobilnej. Możesz uzyskać identyfikator klienta z **zaawansowane** w obszarze **ustawień usługi Azure Active Directory** w portalu.
* Zastąp **INSERT klienta-ID-tutaj** z Identyfikatorem klienta, którego skopiowano aplikację native client.
* Zastąp **INSERT PRZEKIEROWANIA-URI-tutaj** z witryny */.auth/login/done* punktu końcowego, za pomocą schematu HTTPS. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done*.

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

Połączenie klienta jest zwykle podstawowe połączenie HTTP za pomocą podstawowej biblioteki HTTP dostarczony wraz z zestawu SDK systemu Android.  Istnieje kilka przyczyn, dlaczego warto zmienić:

* Chcesz limitów czasu za pomocą alternatywnej biblioteki HTTP.
* Możesz podać pasek postępu.
* Chcesz dodać niestandardowego nagłówka do obsługi funkcji zarządzania interfejsu API.
* Chcesz przechwytywać odpowiedzi nie powiodło się, dzięki czemu można zaimplementować ponowne uwierzytelnianie.
* Chcesz rejestrować żądań wewnętrznej bazy danych do usługi analytics.

### <a name="using-an-alternate-http-library"></a>Za pomocą alternatywnej biblioteki HTTP

Wywołanie `.setAndroidHttpClientFactory()` metody natychmiast po utworzeniu odwołanie do klienta.  Na przykład, aby ustawić limit czasu połączenia 60 sekund (zamiast domyślnej 10 sekund):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClinet();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementuje filtr postępu

ODCIĘTA każde żądanie można zaimplementować zaimplementowanie `ServiceFilter`.  Na przykład następujące aktualizuje pasek postępu wstępnie utworzone:

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
                    pubic void run() {
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

### <a name="customize-request-headers"></a>Dostosowywanie nagłówki żądania

Należy użyć następującego `ServiceFilter` i Dołącz filtr w taki sam sposób jak `ProgressFilter`:

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

Można określić strategii konwersji, która ma zastosowanie do wszystkich kolumn za pomocą [gson] [ 3] interfejsu API. Biblioteka systemu Android klienta używa [gson] [ 3] w tle do serializacji obiektów języka Java na dane JSON przed wysłaniem danych do usługi Azure App Service.  Poniższy kod używa **setFieldNamingStrategy()** metody do określania strategii. W tym przykładzie spowoduje usunięcie początkowy znak ("m"), a następnie małe następny znak, dla każdej nazwy pola. Na przykład go spowoduje przekształcenie "mId" w "id".  Wdrożenie strategii konwersji, aby ograniczyć potrzebę `SerializedName()` adnotacje w większości pól.

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
        .setFieldNamingStrategy(namingStategy)
);
```

Ten kod musi zostać wykonana przed utworzeniem odwołania klientów urządzeń przenośnych przy użyciu **MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Rozpoczynanie pracy z uwierzytelnianiem]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: http://www.odata.org/documentation/odata-version-3-0/
[20]: http://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html
