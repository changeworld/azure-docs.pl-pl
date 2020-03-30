---
title: Jak korzystać z SDK dla Androida
description: Jak korzystać z sdk aplikacji mobilnych platformy Azure dla systemu Android
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249388"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak korzystać z sdk aplikacji mobilnych platformy Azure dla systemu Android

W tym przewodniku pokazano, jak za pomocą SDK klienta systemu Android dla aplikacji mobilnych do zaimplementowania typowych scenariuszy, takich jak:

* Wykonywanie zapytań dotyczących danych (wstawianie, aktualizowanie i usuwanie).
* Uwierzytelnianie.
* Obsługa błędów.
* Dostosowywanie klienta.

Ten przewodnik koncentruje się na sdk systemu Android po stronie klienta.  Aby dowiedzieć się więcej o skusikach SDK po stronie serwera dla aplikacji mobilnych, zobacz [Praca z modułem SDK wewnętrznej bazy danych .NET][10] lub [Jak używać sDK wewnętrznej bazy danych Node.js][11].

## <a name="reference-documentation"></a>Dokumentacja referencyjna

Odwołanie do [interfejsu API Javadocs][12] dla biblioteki klienta systemu Android można znaleźć w usłudze GitHub.

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw SDK aplikacji mobilnych platformy Azure dla systemu Android obsługuje poziomy interfejsu API od 19 do 24 (KitKat through Nougat) dla rozmiarów telefonów i tabletów.  Uwierzytelnianie, w szczególności, wykorzystuje podejście common web framework do zbierania poświadczeń.  Uwierzytelnianie przepływu serwera nie działa z urządzeniami o małych rozmiarach, takimi jak zegarki.

## <a name="setup-and-prerequisites"></a>Ustawienia i wymagania wstępne

Ukończ samouczek [Szybki start aplikacji mobilnych.](app-service-mobile-android-get-started.md)  To zadanie gwarantuje, że wszystkie wymagania wstępne dotyczące tworzenia aplikacji mobilnych platformy Azure zostały spełnione.  Szybki start pomaga również skonfigurować konto i utworzyć pierwszą zaplecze aplikacji mobilnej.

Jeśli zdecydujesz się nie ukończyć samouczka Szybki start, wykonaj następujące zadania:

* [utworzyć zaplecze aplikacji mobilnej][13] do użycia z aplikacją na Androida.
* W Android Studio [zaktualizuj pliki kompilacji Gradle](#gradle-build).
* [Włącz uprawnienie do Internetu](#enable-internet).

### <a name="update-the-gradle-build-file"></a><a name="gradle-build"></a>Aktualizowanie pliku kompilacji Gradle

Zmień oba pliki **build.gradle:**

1. Dodaj ten kod do pliku **build.gradle** na poziomie *projektu:*

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

2. Dodaj ten kod do pliku **build.gradle** na poziomie *aplikacji modułu* wewnątrz tagu *zależności:*

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Obecnie najnowsza wersja to 3.4.0. Obsługiwane wersje są wymienione [na bintray][14].

### <a name="enable-internet-permission"></a><a name="enable-internet"></a>Włącz uprawnienie do Internetu

Aby uzyskać dostęp do platformy Azure, aplikacja musi mieć włączone uprawnienia INTERNET. Jeśli nie jest jeszcze włączona, dodaj następujący wiersz kodu do pliku **AndroidManifest.xml:**

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Tworzenie połączenia klienta

Usługa Azure Mobile Apps udostępnia aplikacji mobilnej cztery funkcje:

* Dostęp do danych i synchronizacja w trybie offline z usługą Azure Mobile Apps.
* Wywoływanie niestandardowych interfejsów API napisanych za pomocą sdk serwera usługi Azure Mobile Apps Server.
* Uwierzytelnianie za pomocą uwierzytelniania i autoryzacji usługi Azure App Service.
* Rejestracja powiadomień wypychanych w centrach powiadomień.

Każda z tych funkcji wymaga `MobileServiceClient` najpierw utworzenia obiektu.  Tylko `MobileServiceClient` jeden obiekt powinien zostać utworzony w ramach klienta mobilnego (oznacza to, że powinien być wzorzec Singleton).  Aby utworzyć `MobileServiceClient` obiekt:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

Jest `<MobileAppUrl>` to ciąg lub obiekt adresu URL, który wskazuje na zapleczu telefonu komórkowego.  Jeśli używasz usługi Azure App Service do hostowania zaplecza `https://` mobilnego, upewnij się, że używasz bezpiecznej wersji adresu URL.

Klient wymaga również dostępu do działania `this` lub kontekstu — parametr w przykładzie.  MobileServiceClient budowy powinny odbywać się w ramach `onCreate()` `AndroidManifest.xml` metody działania, do którego odwołuje się w pliku.

Najlepszym rozwiązaniem należy abstrakcyjną komunikację serwera do własnej klasy (wzorzec singleton).  W takim przypadku należy przekazać działanie w konstruktorze, aby odpowiednio skonfigurować usługę.  Przykład:

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

Teraz możesz `AzureServiceAdapter.Initialize(this);` zadzwonić `onCreate()` w metodzie swojej głównej aktywności.  Wszelkie inne metody wymagające dostępu `AzureServiceAdapter.getInstance();` do klienta, aby uzyskać odwołanie do karty usługi.

## <a name="data-operations"></a>Operacje na danych

Rdzeniem zestawu SDK aplikacji mobilnych platformy Azure jest zapewnienie dostępu do danych przechowywanych w usłudze SQL Azure w wewnętrznej ce podejem aplikacji mobilnej.  Dostęp do tych danych można uzyskać przy użyciu silnie typizowanych klas (preferowanych) lub kwerend bez typu (nie zalecane).  Większość tej sekcji dotyczy używania klas silnie typizowane.

### <a name="define-client-data-classes"></a>Definiowanie klas danych klienta

Aby uzyskać dostęp do danych z tabel sql azure, należy zdefiniować klasy danych klienta, które odpowiadają tabelom w wewnętrznej cepocie aplikacji mobilnej. Przykłady w tym temacie zakładają tabelę o nazwie **MyDataTable**, która ma następujące kolumny:

* id
* tekst
* kończenie

Odpowiedni wpisany obiekt po stronie klienta znajduje się w pliku o nazwie **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Add getter and setter methods for each field that you add.  Jeśli twoja tabela SQL Azure zawiera więcej kolumn, należy dodać odpowiednie pola do tej klasy.  Na przykład jeśli DTO (obiekt transferu danych) miał kolumnę Priorytet liczby całkowitej, można dodać to pole wraz z jego metodami metody odchudnika i ustawiacza:

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

Aby dowiedzieć się, jak utworzyć dodatkowe tabele w wewnętrznej cemie aplikacji mobilnych, zobacz [Jak: Definiowanie kontrolera tabel][15] (backend.NET) lub [Definiowanie tabel przy użyciu schematu dynamicznego][16] (zaplecze node.js).

Tabela wewnętrznej bazy danych usługi Azure Mobile Apps definiuje pięć specjalnych pól, z których cztery są dostępne dla klientów:

* `String id`: Unikatowy identyfikator rekordu na całym świecie.  Najlepszym rozwiązaniem, aby identyfikator String reprezentacji obiektu [UUID.][17]
* `DateTimeOffset updatedAt`: Data/godzina ostatniej aktualizacji.  Pole updatedAt jest ustawiane przez serwer i nigdy nie powinno być ustawiane przez kod klienta.
* `DateTimeOffset createdAt`: Data/godzina utworzenia obiektu.  Pole createdAt jest ustawiane przez serwer i nigdy nie powinno być ustawiane przez kod klienta.
* `byte[] version`: Normalnie reprezentowany jako ciąg, wersja jest również ustawiana przez serwer.
* `boolean deleted`: Wskazuje, że rekord został usunięty, ale nie został jeszcze usunięty.  Nie należy `deleted` używać jako właściwości w klasie.

Pole `id` jest wymagane.  Pole `updatedAt` i `version` pole są używane do synchronizacji w trybie offline (odpowiednio dla synchronizacji przyrostowej i rozwiązywania konfliktów).  To `createdAt` pole jest polem referencyjnym i nie jest używane przez klienta.  Nazwy są "przez sieć" nazwy właściwości i nie są regulowane.  Można jednak utworzyć mapowanie między obiektem a nazwami "across-the-wire" przy użyciu biblioteki [gson.][3]  Przykład:

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

Aby uzyskać dostęp do tabeli, należy najpierw utworzyć obiekt [MobileServiceTable,][8] wywołując metodę **getTable** w [aplikacji MobileServiceClient][9].  Ta metoda ma dwa przeciążenia:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

W poniższym kodzie **mClient** jest odwołaniem do obiektu MobileServiceClient.  Pierwsze przeciążenie jest używane, gdy nazwa klasy i nazwa tabeli są takie same i jest używana w przewodniku Szybki start:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Drugie przeciążenie jest używane, gdy nazwa tabeli różni się od nazwy klasy: pierwszym parametrem jest nazwa tabeli.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query-a-backend-table"></a><a name="query"></a>Zapytanie o tabelę wewnętrznej bazy danych

Najpierw uzyskaj odwołanie do tabeli.  Następnie wykonaj kwerendę w odwołaniu do tabeli.  Kwerenda to dowolna kombinacja:

* `.where()` [Klauzula filtru](#filtering).
* `.orderBy()` [Klauzula zamówienia](#sorting).
* `.select()` [Klauzula wyboru pola](#selection).
* A `.skip()` `.top()` i [dla wyników stronicowa.](#paging)

Klauzule muszą być przedstawione w powyższej kolejności.

### <a name="filtering-results"></a><a name="filter"></a>Wyniki filtrowania

Ogólna forma kwerendy jest:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

W poprzednim przykładzie zwraca wszystkie wyniki (maksymalnie ustawiony rozmiar strony ustawiony przez serwer).  Metoda `.execute()` wykonuje kwerendę na wewnętrznej bazy danych.  Kwerenda jest konwertowana na kwerendę [OData v3][19] przed transmisją do zaplecza aplikacji mobilnych.  Po otrzymaniu wewnętrznej bazy danych aplikacji mobilnych konwertuje kwerendę do instrukcji SQL przed wykonaniem go w wystąpieniu platformy SQL Azure.  Ponieważ aktywność sieciowa `.execute()` zajmuje trochę [`ListenableFuture<E>`][18]czasu, Metoda zwraca .

### <a name="filter-returned-data"></a><a name="filtering"></a>Filtr zwracane dane

Następujące wykonanie kwerendy zwraca wszystkie elementy z tabeli **ToDoItem,** gdzie **ukończone** jest równe **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** to odwołanie do tabeli usług mobilnych, którą utworzyliśmy wcześniej.

Zdefiniuj filtr przy użyciu wywołania metody **where** w odwołaniu do tabeli. Gdzie **where** metoda następuje metoda **pola** następuje metoda, która określa logiczne predykatu. Możliwe metody predykatu obejmują **eq** (równa się), **ne** (nie równe), **gt** (większe niż), **ge** (większe lub równe), **lt** (mniejsze niż), **le** (mniejsze lub równe). Te metody umożliwiają porównywanie pól liczbowych i ciągów z określonymi wartościami.

Można filtrować według dat. Następujące metody umożliwiają porównanie całego pola daty lub części daty: **rok,** **miesiąc,** **dzień,** **godzina,** **minuta**i **druga**. W poniższym przykładzie dodano filtr dla towarów, których *data ukończenia* jest równa 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Następujące metody obsługują złożone filtry w polach **ciągów: startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**i **length**. Poniższy przykład filtruje wiersze tabeli, w których kolumna *tekstowa* zaczyna się od "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Następujące metody operatora są obsługiwane na polach liczbowych: **dodaj**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling**i **round**. Poniższy przykład filtruje wiersze tabeli, w których **czas trwania** jest liczbą parzystą.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Można połączyć predykaty z tymi metodami logicznymi: **i**, **lub** **i nie**. Poniższy przykład łączy dwa z powyższych przykładów.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Grupowanie i zagnieżdżanie operatorów logicznych:

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

Aby uzyskać bardziej szczegółowe omówienie i przykłady filtrowania, zobacz [Eksplorowanie bogactwa modelu zapytań klienta systemu Android][20].

### <a name="sort-returned-data"></a><a name="sorting"></a>Sortowanie zwróconych danych

Poniższy kod zwraca wszystkie elementy z tabeli **ToDoItems posortowane** rosnąco według pola *tekstowego.* *mToDoTable* jest odwołaniem do tabeli wewnętrznej bazy danych utworzonej wcześniej:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Pierwszy parametr **metody orderBy** jest ciągiem równym nazwie pola, na którym ma być sortowana. Drugi parametr używa **wyliczenia QueryOrder,** aby określić, czy sortować rosnąco lub malejąco.  Jeśli filtrujesz przy użyciu ***metody where,*** ***metoda where*** musi być wywoływana przed ***metodą orderBy.***

### <a name="select-specific-columns"></a><a name="selection"></a>Zaznaczanie określonych kolumn

Poniższy kod ilustruje sposób zwracania wszystkich elementów z tabeli **ToDoItems**, ale wyświetla tylko pola **kompletne** i **tekstowe.** **mToDoTable** jest odwołaniem do tabeli wewnętrznej bazy danych, którą utworzyliśmy wcześniej.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry funkcji select są nazwami ciągów kolumn tabeli, które chcesz zwrócić.  Select **select** metoda musi być zgodna z metodami, takimi jak **gdzie** i **orderBy**. Może on a następnie metody stronicowania, takie jak **skip** i **top**.

### <a name="return-data-in-pages"></a><a name="paging"></a>Zwracanie danych na stronach

Dane **są zawsze** zwracane na stronach.  Maksymalna liczba zwróconych rekordów jest ustawiana przez serwer.  Jeśli klient zażąda więcej rekordów, serwer zwraca maksymalną liczbę rekordów.  Domyślnie maksymalny rozmiar strony na serwerze wynosi 50 rekordów.

W pierwszym przykładzie pokazano, jak wybrać pięć pierwszych elementów z tabeli. Kwerenda zwraca elementy z tabeli **ToDoItems**. **mToDoTable** jest odwołaniem do tabeli wewnętrznej bazy danych utworzonej wcześniej:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Oto kwerenda, która pomija pierwsze pięć elementów, a następnie zwraca następne pięć:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Jeśli chcesz uzyskać wszystkie rekordy w tabeli, zaimplementuj kod, aby iterować na wszystkich stronach:

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

Żądanie dla wszystkich rekordów przy użyciu tej metody tworzy co najmniej dwa żądania do zaplecza aplikacji mobilnych.

> [!TIP]
> Wybór odpowiedniego rozmiaru strony jest równowaga między użyciem pamięci podczas żądania dzieje, wykorzystanie przepustowości i opóźnienia w odbieraniu danych całkowicie.  Domyślna wartość (50 rekordów) jest odpowiednia dla wszystkich urządzeń.  Jeśli pracujesz wyłącznie na większych urządzeniach pamięciowych, zwiększ do 500.  Odkryliśmy, że zwiększenie rozmiaru strony powyżej 500 rekordów powoduje niedopuszczalne opóźnienia i duże problemy z pamięcią.

### <a name="how-to-concatenate-query-methods"></a><a name="chaining"></a>Jak: Łączenie metod kwerendy

Metody używane w kwerendach tabel wewnętrznej bazy danych mogą być łączone. Metody kwerend łańcuchowych umożliwiają wybranie określonych kolumn filtrowanych wierszy, które są sortowane i stronicowane. Można tworzyć złożone filtry logiczne.  Każda metoda kwerendy zwraca obiekt Query. Aby zakończyć serię metod i faktycznie uruchomić kwerendę, wywołaj metodę **execute.** Przykład:

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

Metody kwerend łańcuchowych należy uporządkować w następujący sposób:

1. Metody filtrowania **(gdzie).**
2. Sortowanie **(orderBy)** metody.
3. Wybór **(wybierz**) metody.
4. metody stronicowania **(pomiń** i **u góry).**

## <a name="bind-data-to-the-user-interface"></a><a name="binding"></a>Powiąż dane z interfejsem użytkownika

Powiązanie danych obejmuje trzy składniki:

* Źródło danych
* Układ ekranu
* Adapter, który łączy te dwa.

W naszym przykładowym kodzie zwracamy dane z tabeli Sql Azure aplikacji mobilnych **DoDoItem** do tablicy. To działanie jest typowym wzorcem dla aplikacji danych.  Kwerendy bazy danych często zwracają kolekcję wierszy, które klient dostaje na liście lub tablicy. W tym przykładzie tablica jest źródłem danych.  Kod określa układ ekranu, który definiuje widok danych wyświetlanych na urządzeniu.  Dwa są powiązane z kartą, która w tym kodzie jest rozszerzeniem **ArrayAdapter&lt;&gt; ToDoItem** klasy.

#### <a name="define-the-layout"></a><a name="layout"></a>Definiowanie układu

Układ jest zdefiniowany przez kilka fragmentów kodu XML. Biorąc pod uwagę istniejący układ, poniższy kod reprezentuje **ListView,** który chcemy wypełnić naszymi danymi serwera.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

W poprzednim kodzie atrybut *listitem* określa identyfikator układu dla pojedynczego wiersza na liście. Ten kod określa pole wyboru i skojarzony z nim tekst i otrzymuje jednocześnie wystąpienia dla każdego elementu na liście. Ten układ nie wyświetla pola **id,** a bardziej złożony układ określałby dodatkowe pola na ekranie. Ten kod znajduje się w pliku **row_list_to_do.xml.**

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

#### <a name="define-the-adapter"></a><a name="adapter"></a>Definiowanie karty
Ponieważ źródłem danych naszego widoku jest tablica **ToDoItem**, my podklasy naszej karty z **ArrayAdapter&lt;ToDoItem&gt; ** klasy. Ta podklasa tworzy Widok dla każdego **ToDoItem** przy użyciu **układu row_list_to_do.**  W naszym kodzie definiujemy następującą klasę, która jest rozszerzeniem klasy **&lt;ArrayAdapter&gt; E:**

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Zastąpokaj karty **getView** metody. Przykład:

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

Tworzymy wystąpienie tej klasy w naszym Działaniu w następujący sposób:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Drugi parametr do Konstruktora ToDoItemAdapter jest odwołanie do układu. Możemy teraz utworzyć wystąpienie **listview** i przypisać kartę do **ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-the-adapter-to-bind-to-the-ui"></a><a name="use-adapter"></a>Użyj adaptera do powiązania z interfejsem użytkownika

Teraz można przystąpić do użycia powiązania danych. Poniższy kod pokazuje, jak uzyskać elementy w tabeli i wypełnia kartę lokalną zwracanych elementów.

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

Wywołanie karty za każdym razem, gdy zmodyfikujesz tabelę **ToDoItem.** Ponieważ modyfikacje są wykonywane na podstawie rekordu według rekordu, obsługiwać jeden wiersz zamiast kolekcji. Po wstawieniu elementu wywołaj metodę **dodawania** na karcie; podczas usuwania wywołać metodę **usuwania.**

Pełny przykład można znaleźć w [programie Szybki start systemu Android][21].

## <a name="insert-data-into-the-backend"></a><a name="inserting"></a>Wstawianie danych do wewnętrznej bazy danych

Tworzenie wystąpienia klasy *ToDoItem* i ustawianie jej właściwości.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Następnie użyj **insert(),** aby wstawić obiekt:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Zwrócona encja jest zgodna z danymi wstawionymi do tabeli wewnętrznej bazy `createdAt` `updatedAt`danych, `version` z uwzględnieniem identyfikatora i innych wartości (takich jak , i pola) ustawionych na wewnętrznej podstawie.

Tabele aplikacji mobilnych wymagają kolumny klucza podstawowego o nazwie **id**. Ta kolumna musi być ciągiem. Domyślną wartością kolumny identyfikatora jest identyfikator GUID.  Możesz podać inne unikatowe wartości, takie jak adresy e-mail lub nazwy użytkowników. Jeśli wartość identyfikatora ciągu nie jest podana dla wstawionego rekordu, wewnętrznej bazy danych generuje nowy identyfikator GUID.

Wartości identyfikatora ciągu zapewniają następujące zalety:

* Identyfikatory mogą być generowane bez dokonywania podróży w obie strony do bazy danych.
* Rekordy są łatwiejsze do scalania z różnych tabel lub baz danych.
* Wartości identyfikatorów lepiej integrują się z logiką aplikacji.

Wartości identyfikatorów ciągów są **wymagane** dla obsługi synchronizacji w trybie offline.  Nie można zmienić identyfikatora, gdy jest przechowywany w bazie danych wewnętrznej bazy danych.

## <a name="update-data-in-a-mobile-app"></a><a name="updating"></a>Aktualizowanie danych w aplikacji mobilnej

Aby zaktualizować dane w tabeli, przekaż nowy obiekt do metody **update().**

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

W tym *przykładzie element* jest odwołaniem do wiersza w tabeli *ToDoItem,* który miał pewne zmiany wprowadzone do niego.  Wiersz o tym samym **identyfikatorze** jest aktualizowany.

## <a name="delete-data-in-a-mobile-app"></a><a name="deleting"></a>Usuwanie danych w aplikacji mobilnej

Poniższy kod pokazuje, jak usunąć dane z tabeli, określając obiekt danych.

```java
mToDoTable
    .delete(item);
```

Element można również usunąć, określając pole **identyfikatora** wiersza do usunięcia.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="look-up-a-specific-item-by-id"></a><a name="lookup"></a>Szukaj określonego elementu według identyfikatora

Wyszukaj element z określonym **polem identyfikatorowym** za pomocą metody **lookUp():**

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="how-to-work-with-untyped-data"></a><a name="untyped"></a>Jak: Praca z danymi bez typu

Model programowania bez typu zapewnia dokładną kontrolę nad serializacją JSON.  Istnieje kilka typowych scenariuszy, w których można użyć modelu programowania bez typu. Na przykład jeśli tabela wewnętrznej bazy danych zawiera wiele kolumn i wystarczy odwołać się tylko do podzbioru kolumn.  Typowany model wymaga zdefiniowania wszystkich kolumn zdefiniowanych w wewnętrznej cemie aplikacji mobilnych w klasie danych.  Większość wywołań interfejsu API dostępu do danych są podobne do maszyn programowania wpisane. Główną różnicą jest to, że w modelu bez typu można wywołać metody na **MobileServiceJsonTable** obiektu, zamiast **MobileServiceTable** obiektu.

### <a name="create-an-instance-of-an-untyped-table"></a><a name="json_instance"></a>Tworzenie wystąpienia tabeli bez typu

Podobnie jak model wpisany, należy rozpocząć od uzyskania odwołania do tabeli, ale w tym przypadku jest to **MobileServicesJsonTable** obiektu. Uzyskaj odwołanie, wywołując **getTable** metody na wystąpienie klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po utworzeniu wystąpienia **MobileServiceJsonTable,** ma praktycznie ten sam interfejs API dostępne jak w przypadku modelu programowania wpisane. W niektórych przypadkach metody przyjmują parametr bez typu zamiast wpisanego parametru.

### <a name="insert-into-an-untyped-table"></a><a name="json_insert"></a>Wstawianie do tabeli bez typu
Poniższy kod pokazuje, jak wykonać wstawianie. Pierwszym krokiem jest utworzenie [JsonObject][1], który jest częścią biblioteki [gson.][3]

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Następnie użyj **insert(),** aby wstawić obiekt bez typu do tabeli.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Jeśli chcesz uzyskać identyfikator wstawionego obiektu, użyj metody **getAsJsonPrimitive().**

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="delete-from-an-untyped-table"></a><a name="json_delete"></a>Usuwanie z tabeli bez typu
Poniższy kod pokazuje, jak usunąć wystąpienie, w tym przypadku to samo wystąpienie **JsonObject,** który został utworzony w poprzednim *przykładzie wstawiania.* Kod jest taki sam jak w przypadku typizowanej sprawy, ale metoda ma inny podpis, ponieważ odwołuje się do **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Wystąpienie można również usunąć bezpośrednio przy użyciu jego identyfikatora:

```java
mToDoTable.delete(ID);
```

### <a name="return-all-rows-from-an-untyped-table"></a><a name="json_get"></a>Zwracanie wszystkich wierszy z tabeli bez typu
Poniższy kod pokazuje, jak pobrać całą tabelę. Ponieważ używasz tabeli JSON, można selektywnie pobrać tylko niektóre kolumny tabeli.

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

Ten sam zestaw metod filtrowania, filtrowania i stronicowania, które są dostępne dla typizowanego modelu, jest dostępny dla modelu bez typu.

## <a name="implement-offline-sync"></a><a name="offline-sync"></a>Implementowanie synchronizacji w trybie offline

Zestaw SDK klienta usługi Azure Mobile Apps implementuje również synchronizację danych w trybie offline przy użyciu bazy danych SQLite do przechowywania kopii danych serwera lokalnie.  Operacje wykonywane w tabeli w trybie offline nie wymagają łączności mobilnej do pracy.  Synchronizacja w trybie offline pomaga w odporności i wydajności kosztem bardziej złożonej logiki rozwiązywania konfliktów.  Zestaw SDK klienta usługi Azure Mobile Apps implementuje następujące funkcje:

* Synchronizacja przyrostowa: pobierane są tylko zaktualizowane i nowe rekordy, co pozwala zaoszczędzić przepustowość i zużycie pamięci.
* Optymistyczna współbieżność: Zakłada się, że operacje zakończy się pomyślnie.  Rozwiązywanie konfliktów jest odroczone, dopóki aktualizacje są wykonywane na serwerze.
* Rozwiązywanie konfliktów: SDK wykrywa, kiedy konflikt zmiany została wykonywalna na serwerze i zapewnia haki, aby ostrzec użytkownika.
* Usuwanie nietrwałe: usunięte rekordy są oznaczane jako usunięte, co pozwala innym urządzeniom na aktualizowanie pamięci podręcznej trybu offline.

### <a name="initialize-offline-sync"></a>Inicjowanie synchronizacji w trybie offline

Każda tabela trybu offline musi być zdefiniowana w pamięci podręcznej trybu offline przed użyciem.  Zwykle definicja tabeli odbywa się natychmiast po utworzeniu klienta:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Uzyskiwanie odwołania do tabeli pamięci podręcznej trybu offline

W przypadku tabeli `.getTable()`online używasz pliku .  W przypadku tabeli `.getSyncTable()`w trybie offline należy użyć:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Wszystkie metody, które są dostępne dla tabel online (w tym filtrowanie, sortowanie, stronicowanie, wstawianie danych, aktualizowanie danych i usuwanie danych) działają równie dobrze w tabelach online i offline.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizowanie lokalnej pamięci podręcznej w trybie offline

Synchronizacja znajduje się pod kontrolą aplikacji.  Oto przykładowa metoda synchronizacji:

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

Jeśli nazwa kwerendy jest `.pull(query, queryname)` podana do metody, a następnie przyrostowe synchronizacji jest używany do zwracania tylko rekordy, które zostały utworzone lub zmienione od ostatniego pomyślnie zakończonego ściągania.

### <a name="handle-conflicts-during-offline-synchronization"></a>Obsługa konfliktów podczas synchronizacji w trybie offline

Jeśli konflikt występuje `.push()` podczas operacji, a `MobileServiceConflictException` jest generowany.   Element wystawiony przez serwer jest osadzony w wyjątku `.getItem()` i może być pobierany przez wyjątek.  Dostosuj wypychanie, wywołując następujące elementy w obiekcie MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Gdy wszystkie konflikty są oznaczone `.push()` zgodnie z życzeniem, zadzwoń ponownie, aby rozwiązać wszystkie konflikty.

## <a name="call-a-custom-api"></a><a name="custom-api"></a>Wywoływanie niestandardowego interfejsu API

Niestandardowy interfejs API umożliwia definiowanie niestandardowych punktów końcowych, które udostępniają funkcje serwera, który nie jest mapowany na operację wstawiania, aktualizowania, usuwania lub odczytu. Za pomocą niestandardowego interfejsu API, można mieć większą kontrolę nad wiadomości, w tym odczytu i ustawiania nagłówków wiadomości HTTP i definiowanie formatu treści wiadomości innych niż JSON.

Z klienta systemu Android, wywołać **invokeApi** metody wywołać niestandardowy punkt końcowy interfejsu API. W poniższym przykładzie pokazano, jak wywołać punkt końcowy interfejsu API o nazwie **completeAll**, który zwraca klasę kolekcji o nazwie **MarkAllResult**.

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

Metoda **invokeApi** jest wywoływana na kliencie, który wysyła żądanie POST do nowego niestandardowego interfejsu API. Wynik zwracany przez niestandardowy interfejs API jest wyświetlany w oknie dialogowym wiadomości, podobnie jak wszelkie błędy. Inne wersje **invokeApi** umożliwiają opcjonalnie wysłanie obiektu w treści żądania, określenie metody HTTP i wysłanie parametrów zapytania za pomocą żądania. Nieotypedyzowanych wersji **invokeApi** są również dostarczane.

## <a name="add-authentication-to-your-app"></a><a name="authentication"></a>Dodawanie uwierzytelniania do aplikacji

Samouczki już szczegółowo opisują, jak dodać te funkcje.

Usługa App Service obsługuje [uwierzytelnianie użytkowników aplikacji](app-service-mobile-android-get-started-users.md) przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, Microsoft Account, Twitter i Azure Active Directory. Uprawnienia do tabel można ustawić w celu ograniczenia dostępu dla określonych operacji tylko do uwierzytelnionych użytkowników. Można również użyć tożsamości uwierzytelnionych użytkowników do zaimplementowania reguł autoryzacji w wewnętrznej bazy danych.

Obsługiwane są dwa przepływy uwierzytelniania: przepływ **serwera** i przepływ **klienta.** Przepływ serwera zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się na interfejsie sieci web dostawców tożsamości.  Do zaimplementowania uwierzytelniania przepływu serwera nie są wymagane żadne dodatkowe pliki SDK. Uwierzytelnianie przepływu serwera nie zapewnia głębokiej integracji z urządzeniem przenośnym i jest zalecane tylko w przypadku scenariuszy weryfikacji koncepcji.

Przepływ klienta umożliwia głębszą integrację z możliwości specyficznych dla urządzenia, takich jak logowanie jednokrotne, ponieważ opiera się na szesnastach dostarczonych przez dostawcę tożsamości.  Możesz na przykład zintegrować sdk Facebooka z aplikacją mobilną.  Klient mobilny zamienia się w aplikację Facebook i potwierdza twoje logowanie przed zamianą z powrotem do aplikacji mobilnej.

Aby włączyć uwierzytelnianie w aplikacji, wymagane są cztery kroki:

* Zarejestruj aplikację do uwierzytelniania u dostawcy tożsamości.
* Konfigurowanie zaplecza usługi aplikacji.
* Ogranicz uprawnienia tabeli do uwierzytelnionych użytkowników tylko w wewnętrznej podstawie danych usługi app.
* Dodaj kod uwierzytelniający do aplikacji.

Uprawnienia do tabel można ustawić w celu ograniczenia dostępu dla określonych operacji tylko do uwierzytelnionych użytkowników. Można również użyć identyfikatora SID uwierzytelnionego użytkownika do modyfikowania żądań.  Aby uzyskać więcej informacji, zapoznaj [się z wprowadzeniem do uwierzytelniania] i dokumentacji HOWTO SDK serwera.

### <a name="authentication-server-flow"></a><a name="caching"></a>Uwierzytelnianie: Przepływ serwera

Poniższy kod uruchamia proces logowania do przepływu serwera za pomocą dostawcy Google.  Dodatkowa konfiguracja jest wymagana ze względu na wymagania dotyczące zabezpieczeń dla dostawcy Google:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Ponadto należy dodać następującą metodę do głównej klasy działania:

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

Zdefiniowane `GOOGLE_LOGIN_REQUEST_CODE` w głównym działania jest `login()` używany dla `onActivityResult()` metody i w ramach metody.  Można wybrać dowolny unikatowy numer, o ile ten `login()` sam `onActivityResult()` numer jest używany w ramach metody i metody.  Jeśli abstrakcyjny kod klienta do karty usługi (jak pokazano wcześniej), należy wywołać odpowiednie metody na karcie usługi.

Należy również skonfigurować projekt dla niestandardowychkat.  Najpierw określ adres URL przekierowania.  Dodaj następujący fragment kodu `AndroidManifest.xml`do:

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

Na koniec `com.android.support:customtabs:28.0.0` dodaj do listy zależności `build.gradle` w pliku:

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

Uzyskaj identyfikator zalogowanego użytkownika z **MobileServiceUser** przy użyciu **metody getUserId.** Na przykład, jak używać futures do wywoływania asynchronicznych interfejsów API logowania, zobacz [Wprowadzenie do uwierzytelniania].

> [!WARNING]
> W wymienionym schemacie adresu URL jest rozróżniana wielkość liter.  Upewnij się, że `{url_scheme_of_you_app}` wszystkie wystąpienia przypadku dopasowania.

### <a name="cache-authentication-tokens"></a><a name="caching"></a>Tokeny uwierzytelniania pamięci podręcznej

Tokeny uwierzytelniania buforowania wymaga przechowywania identyfikatora użytkownika i tokenu uwierzytelniania lokalnie na urządzeniu. Przy następnym uruchomieniu aplikacji należy sprawdzić pamięć podręczną, a jeśli te wartości są obecne, można pominąć procedurę logowania i ponownie nawodnić klienta z tymi danymi. Jednak te dane są poufne i powinny być przechowywane zaszyfrowane ze względów bezpieczeństwa w przypadku kradzieży telefonu.  Możesz zobaczyć pełny przykład sposobu buforowania tokenów uwierzytelniania w [sekcji Tokeny uwierzytelniania pamięci podręcznej][7].

Podczas próby użycia wygasłego tokenu otrzymasz nieautoryzowaną odpowiedź *401.* Błędy uwierzytelniania można obsługiwać przy użyciu filtrów.  Filtry przechwytują żądania do wewnętrznej bazy danych usługi aplikacji. Kod filtru testuje odpowiedź dla 401, wyzwala proces logowania, a następnie wznawia żądanie, które wygenerowało 401.

### <a name="use-refresh-tokens"></a><a name="refresh"></a>Używanie tokenów odświeżania

Token zwrócony przez uwierzytelnianie i autoryzację usługi Azure App Service ma zdefiniowany czas życia jednej godziny.  Po tym okresie należy ponownie uwierzytelnić użytkownika.  Jeśli używasz tokenu długotrwałego, który został odebrany za pośrednictwem uwierzytelniania przepływu klienta, możesz ponownie uwierzytelnić za pomocą uwierzytelniania i autoryzacji usługi Azure App Service przy użyciu tego samego tokenu.  Inny token usługi Azure App Service jest generowany z nowym okresem istnienia.

Można również zarejestrować dostawcę, aby użyć tokenów odświeżania.  Token odświeżania nie zawsze jest dostępny.  Wymagana jest dodatkowa konfiguracja:

* W przypadku **usługi Azure Active Directory**skonfiguruj klucz tajny klienta dla aplikacji Usługi Azure Active Directory.  Określ klucz tajny klienta w usłudze Azure App Service podczas konfigurowania uwierzytelniania usługi Azure Active Directory.  Podczas `.login()`wywoływania `response_type=code id_token` , przekazać jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* W **Google**przypadku Google `access_type=offline` przekaż jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* W przypadku **konta** `wl.offline_access` Microsoft wybierz zakres.

Aby odświeżyć `.refreshUser()`token, zadzwoń:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Najlepszym rozwiązaniem jest utworzenie filtru, który wykrywa odpowiedź 401 z serwera i próbuje odświeżyć token użytkownika.

## <a name="log-in-with-client-flow-authentication"></a>Zaloguj się za pomocą uwierzytelniania przepływu klienta

Ogólny proces logowania za pomocą uwierzytelniania przepływu klienta jest następujący:

* Skonfiguruj uwierzytelnianie i autoryzację usługi Azure App Service tak, jak uwierzytelnianie przepływu serwera.
* Zintegruj zestaw SDK dostawcy uwierzytelniania w celu uzyskania tokenu dostępu.
* Wywołać `.login()` metodę w`result` następujący `AuthenticationResult`sposób ( powinna być):

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

Zobacz przykład pełnego kodu w następnej sekcji.

Zastąp `onSuccess()` metodę dowolnym kodem, którego chcesz użyć podczas pomyślnego logowania.  Ciąg `{provider}` jest prawidłowym **dostawcą: aad** (Azure Active Directory), **facebook**, **google**, **microsoftaccount**lub **twitter**.  Jeśli zaimplementowano uwierzytelnianie niestandardowe, można również użyć tagu dostawcy uwierzytelniania niestandardowego.

### <a name="authenticate-users-with-the-active-directory-authentication-library-adal"></a><a name="adal"></a>Uwierzytelnianie użytkowników za pomocą biblioteki uwierzytelniania usługi Active Directory (ADAL)

Biblioteka uwierzytelniania usługi Active Directory (ADAL) służy do logowania użytkowników do aplikacji przy użyciu usługi Azure Active Directory. Za pomocą logowania przepływu klienta jest `loginAsync()` często lepiej przy użyciu metod, ponieważ zapewnia bardziej natywne środowisko użytkownika i pozwala na dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej dla logowania usługi AAD, wykonując samouczek [Jak skonfigurować usługę App Service dla logowania usługi Active Directory.][22] Upewnij się, aby wykonać opcjonalny krok rejestrowania natywnej aplikacji klienckiej.
2. Zainstaluj usługę ADAL, modyfikując plik build.gradle, aby uwzględnić następujące definicje:

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

3. Dodaj następujący kod do aplikacji, dokonując następujących zamienników:

    * Zamień **INSERT-AUTHORITY-HERE** na nazwę dzierżawy, w której zainicjowano aprowizacji aplikacji. Format powinien https://login.microsoftonline.com/contoso.onmicrosoft.combyć .
    * Zamień **INSERT-RESOURCE-ID-HERE** na identyfikator klienta dla wewnętrznej bazy danych aplikacji mobilnej. Identyfikator klienta można uzyskać na karcie **Zaawansowane** w obszarze **Ustawienia usługi Azure Active Directory** w portalu.
    * Zastąp **INSERT-CLIENT-ID-HERE** identyfikatorem klienta skopiowanym z natywnej aplikacji klienckiej.
    * Zastąp **INSERT-REDIRECT-URI-HERE** punktem końcowym *witryny /.auth/login/done,* używając schematu HTTPS. Ta wartość powinna *https://contoso.azurewebsites.net/.auth/login/done*być podobna do .

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

## <a name="adjust-the-client-server-communication"></a><a name="filters"></a>Dostosowywanie komunikacji klient-serwer

Połączenie klienta jest zwykle podstawowym połączeniem HTTP przy użyciu podstawowej biblioteki HTTP dostarczonej z zestawu SDK systemu Android.  Istnieje kilka powodów, dla których chcesz to zmienić:

* Aby dostosować limity czasu, należy użyć alternatywnej biblioteki HTTP.
* Chcesz podać pasek postępu.
* Chcesz dodać niestandardowy nagłówek do obsługi funkcji zarządzania interfejsem API.
* Chcesz przechwycić odpowiedź nie powiodło się, dzięki czemu można zaimplementować ponowneuwierzynienie.
* Chcesz zalogować żądania wewnętrznej bazy danych do usługi analitycznej.

### <a name="using-an-alternate-http-library"></a>Korzystanie z alternatywnej biblioteki HTTP

Wywołanie `.setAndroidHttpClientFactory()` metody natychmiast po utworzeniu odwołania klienta.  Na przykład, aby ustawić limit czasu połączenia na 60 sekund (zamiast domyślnego 10 sekund):

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

### <a name="implement-a-progress-filter"></a>Implementowanie filtru postępu

Można zaimplementować przechwytywanie każdego `ServiceFilter`żądania, implementując plik .  Na przykład następujące aktualizacje wstępnie utworzony pasek postępu:

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

Filtr ten można dołączyć do klienta w następujący sposób:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Dostosowywanie nagłówków żądań

Należy użyć `ServiceFilter` następującego przycisku i przymocować filtr w taki sam `ProgressFilter`sposób, jak:

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

### <a name="configure-automatic-serialization"></a><a name="conversions"></a>Konfigurowanie automatycznej serializacji

Można określić strategię konwersji, która ma zastosowanie do każdej kolumny przy użyciu interfejsu API [gson.][3] Biblioteka klienta systemu Android używa [gson][3] za kulisami do serializacji obiektów Java do danych JSON przed wysłaniem danych do usługi Azure App Service.  Poniższy kod używa **metody setFieldNamingStrategy()** do ustawiania strategii. W tym przykładzie spowoduje usunięcie znaku początkowego ("m"), a następnie małe litery następnego znaku dla każdej nazwy pola. Na przykład zmieni "mId" w "id".  Zaimplementuj strategię `SerializedName()` konwersji, aby zmniejszyć zapotrzebowanie na adnotacje w większości pól.

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

Ten kod musi zostać wykonany przed utworzeniem odwołania do klienta mobilnego przy użyciu **aplikacji MobileServiceClient**.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Wprowadzenie do uwierzytelniania]: app-service-mobile-android-get-started-users.md
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
