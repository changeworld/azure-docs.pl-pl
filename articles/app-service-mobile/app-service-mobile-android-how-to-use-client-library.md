---
title: Jak używać zestawu SDK dla systemu Android
description: Jak używać zestawu Azure Mobile Apps SDK dla systemu Android
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 52e91d900ce0f22862904695ba8adf463219c469
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374230"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Jak używać zestawu Azure Mobile Apps SDK dla systemu Android

W tym przewodniku pokazano, jak używać zestawu SDK klienta systemu Android dla Mobile Apps do implementowania typowych scenariuszy, takich jak:

* Wykonywanie zapytań dotyczących danych (Wstawianie, aktualizowanie i usuwanie).
* Uwierzytelnianie.
* Obsługa błędów.
* Dostosowywanie klienta programu.

Ten przewodnik koncentruje się na Android SDK po stronie klienta.  Aby dowiedzieć się więcej na temat zestawów SDK po stronie serwera dla Mobile Apps, zobacz artykuł [współpraca z zestawem SDK zaplecza platformy .NET][10] lub [Używanie zestawu SDK zaplecza Node. js][11].

## <a name="reference-documentation"></a>Dokumentacja referencyjna

[Dokumentacja interfejsu API Javadocs][12] dla biblioteki klienckiej systemu Android można znaleźć w witrynie GitHub.

## <a name="supported-platforms"></a>Obsługiwane platformy

Zestaw Azure Mobile Apps SDK dla systemu Android obsługuje poziomy interfejsu API od 19 do 24 (KitKat do Nougat) dla urządzeń telefonicznych i tabletów.  Uwierzytelnianie, w szczególności, wykorzystuje wspólne podejście platformy sieci Web do zbierania poświadczeń.  Uwierzytelnianie przepływu serwera nie działa z niewielkimi urządzeniami typu Factor form, takimi jak zegarki.

## <a name="setup-and-prerequisites"></a>Instalacja i wymagania wstępne

Ukończ samouczek [szybkiego startu Mobile Apps](app-service-mobile-android-get-started.md) .  To zadanie gwarantuje, że wszystkie wymagania wstępne dotyczące opracowywania Mobile Apps platformy Azure zostały spełnione.  Przewodnik Szybki Start pomaga w konfigurowaniu konta i tworzeniu pierwszego zaplecza aplikacji mobilnej.

W przypadku rezygnacji z samouczka szybkiego startu wykonaj następujące zadania:

* [Utwórz zaplecze aplikacji mobilnej][13] do użycia z aplikacją systemu Android.
* W Android Studio należy [zaktualizować pliki kompilacji Gradle](#gradle-build).
* [Włącz uprawnienie internetowe](#enable-internet).

### <a name="gradle-build"></a>Aktualizowanie pliku kompilacji Gradle

Zmień pliki **Build. Gradle** :

1. Dodaj ten kod do pliku **Build. Gradle** na poziomie *projektu* :

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

2. Dodaj ten kod do pliku **Build. Gradle** poziomu *aplikacji modułu* wewnątrz tagu *zależności* :

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Obecnie Najnowsza wersja to 3.4.0. Obsługiwane wersje są wymienione [w bintray][14].

### <a name="enable-internet"></a>Włącz uprawnienie internetowe

Aby można było uzyskać dostęp do platformy Azure, aplikacja musi mieć włączone uprawnienie Internetu. Jeśli nie jest jeszcze włączona, Dodaj następujący wiersz kodu do pliku **pliku AndroidManifest. XML** :

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Tworzenie połączenia klienta

Usługa Azure Mobile Apps udostępnia cztery funkcje aplikacji mobilnej:

* Dostęp do danych i synchronizacja w trybie offline za pomocą usługi Azure Mobile Apps.
* Wywoływanie niestandardowych interfejsów API pisanych przy użyciu zestawu Azure Mobile Apps Server SDK.
* Uwierzytelnianie za pomocą uwierzytelniania Azure App Service i autoryzacji.
* Rejestracja powiadomień wypychanych przy użyciu Notification Hubs.

Każda z tych funkcji najpierw wymaga utworzenia obiektu `MobileServiceClient`.  Tylko jeden obiekt `MobileServiceClient` powinien zostać utworzony w ramach klienta mobilnego (to oznacza, że powinien być pojedynczym wzorcem).  Aby utworzyć obiekt `MobileServiceClient`:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` jest ciągiem lub obiektem adresu URL, który wskazuje na zaplecze mobilne.  Jeśli używasz Azure App Service do hostowania zaplecza mobilnego, upewnij się, że używasz bezpiecznej `https://` wersji adresu URL.

Klient wymaga również dostępu do działania lub kontekstu — parametru `this` w przykładzie.  Konstrukcja MobileServiceClient powinna nastąpić w ramach metody `onCreate()` działania, do którego odwołuje się plik `AndroidManifest.xml`.

Najlepszym rozwiązaniem jest zintegrowanie komunikacji serwera z własną klasą (singleton-wzorców).  W takim przypadku należy przekazać działanie w konstruktorze w celu odpowiedniego skonfigurowania usługi.  Na przykład:

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

Teraz można wywołać `AzureServiceAdapter.Initialize(this);` w metodzie `onCreate()` głównego działania.  Wszystkie inne metody wymagające dostępu do klienta używają `AzureServiceAdapter.getInstance();`, aby uzyskać odwołanie do karty usługi.

## <a name="data-operations"></a>Operacje na danych

Rdzeń zestawu Azure Mobile Apps SDK zapewnia dostęp do danych przechowywanych w ramach platformy SQL Azure w zapleczu aplikacji mobilnej.  Możesz uzyskać dostęp do tych danych przy użyciu klas o jednoznacznie określonym typie (preferowany) lub zapytań niebędących nieokreślonymi (niezalecane).  Ta sekcja zawiera zbiorcze transakcje z użyciem klas silnie wpisanych.

### <a name="define-client-data-classes"></a>Definiowanie klas danych klienta

Aby uzyskać dostęp do danych z tabel usługi SQL Azure, zdefiniuj klasy danych klienta odpowiadające tabelom zaplecza aplikacji mobilnej. W przykładach w tym temacie przyjęto założenie, że tabela nosi nazwę "Moja **Data**", która zawiera następujące kolumny:

* id
* tekst
* wykonanie

Odpowiadający wpisanemu obiektowi po stronie klienta znajduje się w pliku o nazwie Moja **Data. Java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Dodaj metody pobierającej i ustawiającej dla każdego dodawanego pola.  Jeśli tabela SQL Azure zawiera więcej kolumn, należy dodać odpowiednie pola do tej klasy.  Na przykład jeśli DTO (obiekt transferu danych) ma kolumnę o priorytecie Integer, można dodać to pole wraz z metodami pobierającymi i setter:

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

Aby dowiedzieć się, jak utworzyć dodatkowe tabele w zapleczu Mobile Apps, zobacz [How to: define the Table Controller][15] (.NET zaplecza) lub [Definiuj tabele przy użyciu schematu dynamicznego][16] (zaplecza Node. js).

Tabela zaplecza Mobile Apps platformy Azure definiuje pięć pól specjalnych, z których cztery są dostępne dla klientów:

* `String id`: unikatowy identyfikator globalny dla rekordu.  Najlepszym rozwiązaniem jest, aby identyfikator był reprezentacją ciągu obiektu [UUID][17] .
* `DateTimeOffset updatedAt`: Data/godzina ostatniej aktualizacji.  Pole updatedAt jest ustawiane przez serwer i nigdy nie powinno być ustawiane przez kod klienta.
* `DateTimeOffset createdAt`: Data/godzina utworzenia obiektu.  Pole createdAt jest ustawiane przez serwer i nigdy nie powinno być ustawiane przez kod klienta.
* `byte[] version`: zwykle reprezentowane jako ciąg, wersja jest również ustawiana przez serwer.
* `boolean deleted`: wskazuje, że rekord został usunięty, ale nie został jeszcze przeczyszczony.  Nie należy używać `deleted` jako właściwości w klasie.

Pole `id` jest wymagane.  Pole `updatedAt` i pole `version` są używane do synchronizacji w trybie offline (odpowiednio w przypadku synchronizacji przyrostowej i rozwiązywania konfliktów).  Pole `createdAt` jest polem odwołania i nie jest używane przez klienta.  Nazwy są nazwami "między sieciami" i nie są dostosowywane.  Można jednak utworzyć mapowanie między obiektem a nazwami "w sieci szkieletowej" przy użyciu biblioteki [gson][3] .  Na przykład:

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

### <a name="create-a-table-reference"></a>Utwórz odwołanie do tabeli

Aby uzyskać dostęp do tabeli, najpierw Utwórz obiekt [MobileServiceTable][8] przez wywołanie metody **GetTable** w [MobileServiceClient][9].  Ta metoda ma dwa przeciążenia:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

W poniższym kodzie **mClient** jest odwołaniem do obiektu MobileServiceClient.  Pierwsze przeciążenie jest używane, gdzie nazwa klasy i nazwa tabeli są takie same i są używane w przewodniku szybki start:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Drugie Przeciążenie jest używane, gdy nazwa tabeli różni się od nazwy klasy: pierwszy parametr jest nazwą tabeli.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Wykonywanie zapytań względem tabeli zaplecza

Najpierw Uzyskaj odwołanie do tabeli.  Następnie wykonaj zapytanie dotyczące odwołania do tabeli.  Zapytanie jest dowolną kombinacją:

* `.where()` [klauzulę filtru](#filtering).
* `.orderBy()` [klauzulę porządkowania](#sorting).
* `.select()` [klauzulę zaznaczania pola](#selection).
* `.skip()` i `.top()` dla [wyników ze strony](#paging).

Klauzule muszą być przedstawione w powyższej kolejności.

### <a name="filter"></a>Filtrowanie wyników

Ogólna postać zapytania:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Poprzedni przykład zwraca wszystkie wyniki (do maksymalnego rozmiaru strony ustawionego przez serwer).  Metoda `.execute()` wykonuje zapytanie w zapleczu.  Zapytanie jest konwertowane na zapytanie [OData V3][19] przed przesłaniem do Mobile Apps zaplecza.  Na paragonie zaplecze Mobile Apps konwertuje zapytanie do instrukcji SQL przed wykonaniem go w wystąpieniu usługi SQL Azure.  Ponieważ działanie sieciowe trwa jakiś czas, Metoda `.execute()` zwraca [`ListenableFuture<E>`][18].

### <a name="filtering"></a>Filtrowanie zwróconych danych

Następujące wykonanie zapytania zwraca wszystkie elementy z tabeli **ToDoItem** , gdzie **Complete** ma wartość **false**.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** to odwołanie do tabeli usługi mobilnej, która została wcześniej utworzona.

Zdefiniuj filtr przy użyciu wywołania metody **WHERE** w odwołaniu do tabeli. Po metodzie **WHERE** następuje Metoda **pola** , a następnie metoda, która określa predykat logiczny. Możliwe metody predykatu **obejmują EQ** (Equals), **ne** (nie równe), **gt** (większe niż), **GE** (większe niż lub równe), **lt** (mniejsze niż), **Le** (mniejsze niż lub równe). Te metody pozwalają porównać liczby i pola ciągów z określonymi wartościami.

Można filtrować według dat. Poniższe metody pozwalają porównać całe pole daty lub części daty: **Year**, **Month**, **Day**, **Hour**, **minute**i **Second**. Poniższy przykład dodaje filtr dla elementów, których termin *ukończenia* jest równy 2013.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Następujące metody obsługują złożone filtry dla pól ciągów: **StartsWith**, **EndsWith**, **concat**, **substring**, **IndexOf**, **replace**, **toLower**, **toUpper**, **Trim**i **Length**. Poniższe przykładowe filtry dla wierszy tabeli, w których kolumna *tekst* rozpoczyna się od "PRI0".

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Następujące metody operatora są obsługiwane w polach liczbowych: **Dodaj**, **Sub**, **MUL**, **DIV**, **mod**, **Floor**, **sufit**i **Round**. Poniższe przykładowe filtry dla wierszy tabeli, w których **czas trwania** jest liczbą parzystą.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Można łączyć predykaty z tymi metodami logicznymi: **and**, **or** i **not**. Poniższy przykład łączy dwa z powyższych przykładów.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Grupuj i Zagnieżdżaj operatory logiczne:

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

Aby uzyskać bardziej szczegółowe omówienie i przykłady filtrowania, zobacz [Eksplorowanie modelu zapytań klienta systemu Android][20].

### <a name="sorting"></a>Sortuj zwrócone dane

Poniższy kod zwraca wszystkie elementy z tabeli **ToDoItems** posortowane rosnąco według pola *tekstowego* . *mToDoTable* jest odwołaniem do utworzonej wcześniej tabeli zaplecza:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Pierwszy parametr metody **OrderBy** jest ciągiem równym nazwie pola, według którego ma zostać wykonane sortowanie. Drugi parametr używa wyliczenia **QueryOrder** , aby określić, czy sortować rosnąco czy malejąco.  Jeśli filtrowanie odbywa się przy użyciu metody ***WHERE*** , Metoda ***WHERE*** musi być wywoływana przed metodą ***OrderBy*** .

### <a name="selection"></a>Wybierz określone kolumny

Poniższy kod ilustruje sposób zwracania wszystkich elementów z tabeli **ToDoItems**, ale wyświetla tylko pola **kompletne** i **Text** . **mToDoTable** jest odwołaniem do tabeli zaplecza, która została utworzona wcześniej.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Parametry funkcji Select to nazwy ciągów kolumn tabeli, które mają zostać zwrócone.  Metoda **SELECT** musi stosować metody, takie jak **WHERE** i **OrderBy**. Może następować metody stronicowania, takie jak **Skip** i **Top**.

### <a name="paging"></a>Zwróć dane na stronach

Dane są **zawsze** zwracane na stronach.  Maksymalna liczba zwracanych rekordów jest ustawiana przez serwer.  Jeśli klient zażąda więcej rekordów, serwer zwróci maksymalną liczbę rekordów.  Domyślnie maksymalny rozmiar strony na serwerze to 50 rekordów.

Pierwszy przykład pokazuje, jak wybrać pięć pierwszych elementów z tabeli. Zapytanie zwraca elementy z tabeli **ToDoItems**. **mToDoTable** jest odwołaniem do utworzonej wcześniej tabeli zaplecza:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Oto zapytanie, które pomija pierwsze pięć elementów, a następnie zwraca kolejne pięć:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Jeśli chcesz pobrać wszystkie rekordy w tabeli, zaimplementuj kod, aby wykonać iterację na wszystkich stronach:

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

Żądanie dotyczące wszystkich rekordów przy użyciu tej metody tworzy co najmniej dwa żądania do Mobile Apps zaplecza.

> [!TIP]
> Wybranie odpowiedniego rozmiaru strony to równowaga między użyciem pamięci, gdy żądanie jest wykonywane, użycie przepustowości i opóźnienie w przypadku otrzymywania danych.  Domyślne (50 rekordy) są odpowiednie dla wszystkich urządzeń.  Jeśli działa wyłącznie na większych urządzeniach pamięci, Zwiększ do 500.  Znaleźliśmy, że zwiększenie rozmiaru strony powyżej 500 rekordów powoduje nieakceptowalne opóźnienia i problemy z dużą ilością pamięci.

### <a name="chaining"></a>Instrukcje: łączenie metod zapytania

Metody używane do wykonywania zapytań w tabelach zaplecza mogą być połączone. Łańcuchowe metody zapytania umożliwiają wybranie konkretnych kolumn filtrowanych wierszy, które są sortowane i stronicowane. Można tworzyć złożone filtry logiczne.  Każda metoda zapytania zwraca obiekt zapytania. Aby zakończyć serię metod i faktycznie uruchomić zapytanie, wywołaj metodę **Execute** . Na przykład:

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

1. Metody filtrowania (**gdzie**).
2. Metody sortowania (**OrderBy**).
3. Metody wyboru (**SELECT**).
4. stronicowanie (**Pomiń** i **Top**) metody.

## <a name="binding"></a>Powiąż dane z interfejsem użytkownika

Powiązanie danych obejmuje trzy składniki:

* Źródło danych
* Układ ekranu
* Karta, która łączy te dwa ze sobą.

W naszym przykładowym kodzie zwracamy dane z tabeli Mobile Apps SQL Azure **ToDoItem** do tablicy. To działanie jest typowym wzorcem dla aplikacji danych.  Zapytania bazy danych często zwracają kolekcję wierszy, które klient pobiera na listę lub tablicę. W tym przykładzie tablica jest źródłem danych.  Kod określa układ ekranu, który definiuje widok danych, które pojawiają się na urządzeniu.  Te dwa są powiązane ze sobą za pomocą karty, która w tym kodzie jest rozszerzeniem klasy **&gt;ArrayAdapter&lt;ToDoItem** .

#### <a name="layout"></a>Definiowanie układu

Układ jest definiowany przez kilka fragmentów kodu XML. Uwzględniając istniejący układ, poniższy kod reprezentuje **element ListView** , który ma zostać wypełniony danymi z serwera.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

W poprzednim kodzie atrybut *ListItem* określa identyfikator układu dla poszczególnych wierszy na liście. Ten kod określa pole wyboru i skojarzony z nim tekst, a następnie tworzy wystąpienie jednokrotne dla każdego elementu na liście. Ten układ nie wyświetla pola **ID** , a bardziej skomplikowany układ określa dodatkowe pola na ekranie. Ten kod znajduje się w pliku **row_list_to_do. XML** .

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

#### <a name="adapter"></a>Zdefiniuj kartę
Ze względu na to, że źródło danych w naszym widoku jest tablicą **ToDoItem**, tworzymy podklasę naszej karty z klasy **ArrayAdapter&lt;ToDoItem&gt;** . Ta podklasa tworzy widok dla każdego **ToDoItemu** przy użyciu układu **row_list_to_do** .  W naszym kodzie definiujemy następującą klasę, która jest rozszerzeniem klasy **ArrayAdapter&lt;E&gt;** :

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Zastąp metodę **GetView** adapterów. Na przykład:

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

Utwórz wystąpienie tej klasy w naszym działaniu w następujący sposób:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Drugim parametrem konstruktora ToDoItemAdapter jest odwołanie do układu. Teraz można utworzyć wystąpienie **elementu ListView** i przypisać kartę do **elementu ListView**.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Użyj karty, aby powiązać z interfejsem użytkownika

Teraz można przystąpić do korzystania z powiązań danych. Poniższy kod przedstawia sposób pobierania elementów z tabeli i wypełniania karty lokalnej z zwróconymi elementami.

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

Wywołaj kartę przy każdej modyfikacji tabeli **ToDoItem** . Ze względu na to, że modyfikacje są wykonywane na podstawie rekordu, należy obsłużyć pojedynczy wiersz zamiast kolekcji. Podczas wstawiania elementu należy wywołać metodę **Add** na karcie; Podczas usuwania należy wywołać metodę **Remove** .

Pełny przykład można znaleźć w [projekcie szybkiego startu systemu Android][21].

## <a name="inserting"></a>Wstawianie danych do zaplecza

Utwórz wystąpienie klasy *ToDoItem* i ustaw jej właściwości.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Następnie użyj **Insert ()** , aby wstawić obiekt:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Zwracana jednostka dopasowuje dane wstawione do tabeli zaplecza, w tym identyfikator i wszelkie inne wartości (takie jak pola `createdAt`, `updatedAt`i `version`) ustawione w zapleczu.

Tabele Mobile Apps wymagają kolumny klucza podstawowego o nazwie **ID**. Ta kolumna musi być ciągiem. Wartość domyślna kolumny ID jest identyfikatorem GUID.  Możesz podać inne unikatowe wartości, takie jak adresy e-mail lub nazwy użytkowników. Jeśli nie podano wartości identyfikatora ciągu dla wstawionego rekordu, zaplecze generuje nowy identyfikator GUID.

Wartości identyfikatora ciągu zapewniają następujące korzyści:

* Identyfikatory można generować bez przeprowadzenia rundy do bazy danych.
* Rekordy są łatwiejsze do scalenia z różnych tabel lub baz danych.
* Wartości identyfikatorów integrują się lepiej z logiką aplikacji.

Do obsługi synchronizacji w trybie offline są **wymagane** wartości identyfikatora ciągu.  Nie można zmienić identyfikatora, gdy jest on przechowywany w bazie danych zaplecza.

## <a name="updating"></a>Aktualizowanie danych w aplikacji mobilnej

Aby zaktualizować dane w tabeli, Przekaż nowy obiekt do metody **Update ()** .

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

W tym przykładzie *element* jest odwołaniem do wiersza w tabeli *ToDoItem* , w którym wprowadzono pewne zmiany.  Wiersz o takim samym **identyfikatorze** został zaktualizowany.

## <a name="deleting"></a>Usuwanie danych w aplikacji mobilnej

Poniższy kod przedstawia sposób usuwania danych z tabeli przez określenie obiektu danych.

```java
mToDoTable
    .delete(item);
```

Możesz również usunąć element, określając pole **Identyfikator** wiersza do usunięcia.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Wyszukiwanie określonego elementu według identyfikatora

Wyszukaj element z określonym polem **identyfikatora** z użyciem metody **lookUp ()** :

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Instrukcje: korzystanie z danych bez typu

Model programowania nietypu zapewnia dokładną kontrolę serializacji JSON.  Istnieją pewne typowe scenariusze, w których warto użyć nietypu modelu programowania. Na przykład jeśli tabela zaplecza zawiera wiele kolumn i musisz tylko odwoływać się do podzbioru kolumn.  Typ model wymaga zdefiniowania wszystkich kolumn zdefiniowanych w zaplecze Mobile Apps w klasie danych.  Większość wywołań interfejsu API do uzyskiwania dostępu do danych jest podobna do typu wywołań programistycznych. Główna różnica polega na tym, że w modelu bez typu wywoływanie metod w obiekcie **MobileServiceJsonTable** zamiast obiektu **MobileServiceTable** .

### <a name="json_instance"></a>Tworzenie wystąpienia tabeli nietypu

Podobnie jak w przypadku typu model, Zacznij od pobrania odwołania do tabeli, ale w tym przypadku jest to obiekt **MobileServicesJsonTable** . Uzyskaj odwołanie poprzez wywołanie metody **GetTable** w wystąpieniu klienta:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Po utworzeniu wystąpienia **MobileServiceJsonTable**ma praktycznie ten sam interfejs API, który jest dostępny w przypadku systemu z określonym modelem programowania. W niektórych przypadkach metody przyjmują parametr bez typu, a nie określony parametr.

### <a name="json_insert"></a>Wstaw do tabeli nietypu
Poniższy kod pokazuje, jak wykonać wstawkę. Pierwszym krokiem jest utworzenie obiektu [JsonObject][1], który jest częścią biblioteki [gson][3] .

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Następnie użyj **Insert ()** , aby wstawić obiekt untyped do tabeli.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Jeśli musisz uzyskać identyfikator wstawionego obiektu, użyj metody **getAsJsonPrimitive ()** .

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Usuń z tabeli nietypu
Poniższy kod pokazuje, jak usunąć wystąpienie, w tym przypadku to to samo wystąpienie elementu **JsonObject** , który został utworzony w poprzednim przykładzie *wstawiania* . Kod jest taki sam jak w przypadku określonego przypadku, ale metoda ma inny podpis, ponieważ odwołuje się do obiektu **JsonObject**.

```java
mToDoTable
    .delete(insertedItem);
```

Można również usunąć wystąpienie bezpośrednio przy użyciu jego identyfikatora:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Zwróć wszystkie wiersze z tabeli niewpisanej
Poniższy kod przedstawia sposób pobierania całej tabeli. Ponieważ używasz tabeli JSON, można selektywnie pobierać tylko niektóre kolumny tabeli.

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

Ten sam zestaw metod filtrowania, filtrowania i stronicowania, które są dostępne dla określonego modelu, jest dostępny dla modelu untypeed.

## <a name="offline-sync"></a>Implementowanie synchronizacji w trybie offline

Zestaw SDK klienta Mobile Apps platformy Azure implementuje także synchronizację danych w trybie offline przy użyciu bazy danych programu SQLite do przechowywania kopii danych serwera lokalnie.  Operacje wykonywane w tabeli offline nie wymagają łączności z urządzeniami przenośnymi.  Pomoc dotycząca synchronizacji w trybie offline w odporności i wydajności kosztem bardziej złożonej logiki do rozwiązywania konfliktów.  Zestaw SDK klienta Mobile Apps platformy Azure implementuje następujące funkcje:

* Synchronizacja przyrostowa: pobierane są tylko zaktualizowane i nowe rekordy, dzięki czemu można zaoszczędzić przepustowość i użycie pamięci.
* Współbieżność optymistyczna: założono, że operacje kończą się powodzeniem.  Rozwiązywanie konfliktów jest odroczone do czasu wykonania aktualizacji na serwerze.
* Rozwiązywanie konfliktów: zestaw SDK wykrywa, kiedy na serwerze wprowadzono sprzeczną zmianę i udostępnia punkty zaczepienia ostrzegania użytkownika.
* Usuwanie nietrwałe: usunięte rekordy są oznaczane jako usunięte, co pozwala innym urządzeniom aktualizować ich pamięć podręczną w trybie offline.

### <a name="initialize-offline-sync"></a>Zainicjuj synchronizację w trybie offline

Każda tabela w trybie offline musi być zdefiniowana w pamięci podręcznej offline przed użyciem.  Zwykle definicja tabeli jest wykonywana natychmiast po utworzeniu klienta:

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

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Uzyskaj odwołanie do tabeli pamięci podręcznej w trybie offline

W przypadku tabeli online należy użyć `.getTable()`.  W przypadku tabeli offline Użyj `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Wszystkie metody, które są dostępne dla tabel online (w tym filtrowanie, sortowanie, stronicowanie, wstawianie danych, aktualizowanie danych i usuwanie danych), działają równie dobrze w tabelach online i offline.

### <a name="synchronize-the-local-offline-cache"></a>Synchronizowanie lokalnej pamięci podręcznej w trybie offline

Synchronizacja jest w obrębie formantu aplikacji.  Oto przykładowa Metoda synchronizacji:

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

Jeśli nazwa zapytania jest podana dla metody `.pull(query, queryname)`, synchronizacja przyrostowa służy do zwrócenia tylko rekordów, które zostały utworzone lub zmienione od czasu ostatniego pomyślnego zakończenia ściągania.

### <a name="handle-conflicts-during-offline-synchronization"></a>Obsługa konfliktów podczas synchronizacji w trybie offline

Jeśli wystąpi konflikt w trakcie operacji `.push()`, zostanie zgłoszony `MobileServiceConflictException`.   Element wystawiony przez serwer jest osadzony w wyjątku i może zostać pobrany przez `.getItem()` na wyjątek.  Dostosuj wypychanie, wywołując następujące elementy w obiekcie MobileServiceSyncContext:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Gdy wszystkie konflikty są oznaczone w żądany sposób, wywołaj `.push()` ponownie, aby rozwiązać wszystkie konflikty.

## <a name="custom-api"></a>Wywoływanie niestandardowego interfejsu API

Niestandardowy interfejs API umożliwia Definiowanie niestandardowych punktów końcowych, które uwidaczniają funkcjonalność serwera, która nie jest mapowana na operację wstawiania, aktualizowania, usuwania lub odczytu. Korzystając z niestandardowego interfejsu API, można mieć większą kontrolę nad wiadomościami, w tym odczytywanie i Ustawianie nagłówków wiadomości HTTP i Definiowanie formatu treści wiadomości innego niż JSON.

Z poziomu klienta systemu Android wywoływana jest metoda **invokeApi** w celu wywołania niestandardowego punktu końcowego interfejsu API. Poniższy przykład pokazuje, jak wywołać punkt końcowy interfejsu API o nazwie **completeAll**, który zwraca klasę kolekcji o nazwie **MarkAllResult**.

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

Metoda **invokeApi** jest wywoływana na kliencie, co powoduje wysłanie żądania post do nowego niestandardowego interfejsu API. W oknie dialogowym komunikatu zostanie wyświetlony wynik zwrócony przez niestandardowy interfejs API, co oznacza błędy. Inne wersje programu **invokeApi** umożliwiają opcjonalne wysyłanie obiektu w treści żądania, Określanie metody http i wysyłanie parametrów zapytania do żądania. Dostępne są również niewpisane wersje **invokeApi** .

## <a name="authentication"></a>Dodawanie uwierzytelniania do aplikacji

Samouczki opisują już szczegółowo, jak dodać te funkcje.

App Service obsługuje [uwierzytelnianie użytkowników aplikacji](app-service-mobile-android-get-started-users.md) przy użyciu różnych zewnętrznych dostawców tożsamości: Facebook, Google, konta Microsoft, Twitter i Azure Active Directory. Możesz ustawić uprawnienia dla tabel, aby ograniczyć dostęp do określonych operacji tylko do użytkowników uwierzytelnionych. Można także użyć tożsamości uwierzytelnionych użytkowników do implementowania reguł autoryzacji w zapleczu.

Obsługiwane są dwa przepływy uwierzytelniania: przepływ **serwera** i przepływ **klienta** . Przepływ serwera zapewnia najprostsze środowisko uwierzytelniania, ponieważ opiera się on na interfejsie sieci Web dostawców tożsamości.  Do zaimplementowania uwierzytelniania przepływu serwera nie są wymagane żadne dodatkowe zestawy SDK. Uwierzytelnianie przepływu serwera nie zapewnia głębokiej integracji z urządzeniem przenośnym i jest zalecane tylko w celu sprawdzenia koncepcji scenariuszy.

Przepływ klienta umożliwia dokładniejszą integrację z funkcjami specyficznymi dla urządzenia, takimi jak logowanie jednokrotne, w zależności od zestawów SDK dostarczonych przez dostawcę tożsamości.  Można na przykład zintegrować zestaw SDK usługi Facebook z aplikacją mobilną.  Klient mobilny przechodzi do aplikacji w serwisie Facebook i potwierdza logowanie przed zainstalowaniem ich w aplikacji mobilnej.

Aby włączyć uwierzytelnianie w aplikacji, wymagane są cztery kroki:

* Zarejestruj aplikację pod kątem uwierzytelniania przy użyciu dostawcy tożsamości.
* Skonfiguruj zaplecze App Service.
* Ogranicz uprawnienia do tabeli tylko dla uwierzytelnionych użytkowników tylko w App Service zaplecza.
* Dodaj kod uwierzytelniania do aplikacji.

Możesz ustawić uprawnienia dla tabel, aby ograniczyć dostęp do określonych operacji tylko do użytkowników uwierzytelnionych. Możesz również użyć identyfikatora SID uwierzytelnionego użytkownika, aby modyfikować żądania.  Aby uzyskać więcej informacji, zobacz [wprowadzenie do uwierzytelniania] i dokumentacja zestawu SDK serwera porady.

### <a name="caching"></a>Uwierzytelnianie: przepływ serwera

Poniższy kod uruchamia proces logowania przepływu serwera przy użyciu dostawcy Google.  Wymagana jest dodatkowa konfiguracja ze względu na wymagania dotyczące zabezpieczeń dostawcy Google:

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

`GOOGLE_LOGIN_REQUEST_CODE` zdefiniowane w Twoim głównym działaniu są używane dla metody `login()` i w ramach metody `onActivityResult()`.  Można wybrać dowolny unikatowy numer, o ile ten sam numer jest używany w metodzie `login()` i metody `onActivityResult()`.  Jeśli kod klienta jest abstrakcyjny do karty usługi (jak pokazano wcześniej), należy wywołać odpowiednie metody na karcie usługi.

Należy również skonfigurować projekt dla customtabs.  Najpierw określ adres URL przekierowania.  Dodaj następujący fragment kodu do `AndroidManifest.xml`:

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

Dodaj **redirectUriScheme** do pliku `build.gradle` aplikacji:

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

Na koniec Dodaj `com.android.support:customtabs:28.0.0` do listy zależności w pliku `build.gradle`:

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

Uzyskaj identyfikator zalogowanego użytkownika z **MobileServiceUser** za pomocą metody **GetUserID** . Aby zapoznać się z przykładem użycia przyszłych metod wywoływania interfejsów API logowania asynchronicznego, zobacz Wprowadzenie do [Wprowadzenie do uwierzytelniania].

> [!WARNING]
> W schemacie adresu URL występuje wielkość liter.  Upewnij się, że wszystkie wystąpienia `{url_scheme_of_you_app}` są zgodne.

### <a name="caching"></a>Tokeny uwierzytelniania pamięci podręcznej

Tokeny uwierzytelniania buforowania wymagają lokalnego przechowywania identyfikatora użytkownika i tokenu uwierzytelniania na urządzeniu. Przy następnym uruchomieniu aplikacji należy sprawdzić pamięć podręczną, a jeśli te wartości są obecne, można pominąć procedurę logowania i ponownie zarejestrować klienta za pomocą tych danych. Jednak te dane są poufne i powinny być przechowywane w postaci zaszyfrowanej w celu zapewnienia bezpieczeństwa w przypadku kradzieży telefonu.  Można zobaczyć kompletny przykład buforowania tokenów uwierzytelniania w [sekcji tokeny uwierzytelniania pamięci podręcznej][7].

Gdy próbujesz użyć wygasłego tokenu, otrzymujesz *401 nieautoryzowaną* odpowiedź. Błędy uwierzytelniania można obsłużyć za pomocą filtrów.  Filtry przechwytuje żądania do App Service zaplecza. Kod filtru sprawdza odpowiedź dla 401, wyzwala proces logowania, a następnie wznawia żądanie, które wygenerowało 401.

### <a name="refresh"></a>Użyj tokenów odświeżania

Token zwrócony przez Azure App Service uwierzytelniania i autoryzacji ma zdefiniowany czas istnienia wynoszący 1 godzinę.  Po upływie tego czasu należy ponownie uwierzytelnić użytkownika.  Jeśli używasz tokenu długotrwałego, który został odebrany przez uwierzytelnianie przepływu klienta, możesz ponownie uwierzytelnić się za pomocą Azure App Service uwierzytelniania i autoryzacji przy użyciu tego samego tokenu.  Inny token Azure App Service jest generowany z nowym okresem istnienia.

Dostawcę można także zarejestrować, aby używać tokenów odświeżania.  Token odświeżania nie jest zawsze dostępny.  Wymagana jest dodatkowa konfiguracja:

* Aby uzyskać **Azure Active Directory**, skonfiguruj klucz tajny klienta dla aplikacji Azure Active Directory.  Określ klucz tajny klienta w Azure App Service podczas konfigurowania uwierzytelniania Azure Active Directory.  Podczas wywoływania `.login()`Przekaż `response_type=code id_token` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* W przypadku usługi **Google**Przekaż `access_type=offline` jako parametr:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Dla **konta Microsoft**wybierz zakres `wl.offline_access`.

Aby odświeżyć token, wywołaj `.refreshUser()`:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Najlepszym rozwiązaniem jest utworzenie filtru, który wykrywa odpowiedź 401 z serwera i próbuje odświeżyć token użytkownika.

## <a name="log-in-with-client-flow-authentication"></a>Logowanie przy użyciu uwierzytelniania za pomocą przepływu klienta

Ogólny proces logowania przy użyciu uwierzytelniania w przepływie klienta jest następujący:

* Skonfiguruj uwierzytelnianie Azure App Service i autoryzację w taki sposób, jak uwierzytelnianie przepływu serwera.
* Zintegruj zestaw SDK dostawcy uwierzytelniania na potrzeby uwierzytelniania, aby utworzyć token dostępu.
* Wywołaj metodę `.login()` w następujący sposób (`result` powinna być `AuthenticationResult`):

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

Zobacz kompletny przykład kodu w następnej sekcji.

Zastąp metodę `onSuccess()` dowolnym kodem, który ma być używany na potrzeby pomyślnego logowania.  Ciąg `{provider}` jest prawidłowym dostawcą: **AAD** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**lub **Twitter**.  W przypadku zaimplementowania uwierzytelniania niestandardowego można również użyć znacznika niestandardowego dostawcy uwierzytelniania.

### <a name="adal"></a>Uwierzytelnianie użytkowników za pomocą Active Directory Authentication Library (ADAL)

Active Directory Authentication Library (ADAL) służy do podpisywania użytkowników w aplikacji przy użyciu Azure Active Directory. Użycie metody logowania przepływu klienta jest często preferowane przy użyciu metod `loginAsync()`, ponieważ zapewnia bardziej natywny sposób działania środowiska użytkownika i umożliwia dodatkowe dostosowanie.

1. Skonfiguruj zaplecze aplikacji mobilnej na potrzeby logowania do usługi AAD, wykonując czynności opisane w samouczku [jak skonfigurować App Service do Active Directory logowania][22] . Pamiętaj, aby ukończyć opcjonalny krok rejestracji natywnej aplikacji klienckiej.
2. Zainstaluj bibliotekę ADAL, modyfikując plik Build. Gradle, aby uwzględnić następujące definicje:

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

3. Dodaj następujący kod do aplikacji, wprowadzając następujące zamiany:

    * Zastąp **ciąg INSERT-Authority-** in nazwą dzierżawy, w której została zainicjowana aplikacja. Format powinien być https://login.microsoftonline.com/contoso.onmicrosoft.com.
    * Zastąp wartość **INSERT-Resource-ID w tym miejscu** identyfikatorem klienta dla zaplecze aplikacji mobilnej. Identyfikator klienta można uzyskać z karty **Zaawansowane** w obszarze **Ustawienia Azure Active Directory** w portalu.
    * Zastąp **ciąg INSERT-Client-ID w tym miejscu** identyfikatorem klienta skopiowanym z natywnej aplikacji klienckiej.
    * Zastąp ciąg **INSERT-redirect-URI — tutaj** z punktem końcowym */.auth/login/done* Twojej witryny przy użyciu schematu https. Ta wartość powinna być podobna do *https://contoso.azurewebsites.net/.auth/login/done* .

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

## <a name="filters"></a>Dostosowanie komunikacji klient-serwer

Połączenie z klientem jest zwykle podstawowym połączeniem HTTP przy użyciu źródłowej biblioteki HTTP dostarczonej z Android SDK.  Istnieje kilka powodów, dla których warto zmienić to:

* Chcesz użyć alternatywnej biblioteki HTTP w celu dostosowania limitów czasu.
* Chcesz podać pasek postępu.
* Chcesz dodać niestandardowy nagłówek do obsługi funkcji API Management.
* Chcesz przechwycić niepomyślną odpowiedź, aby można było zaimplementować ponowne uwierzytelnianie.
* Chcesz rejestrować żądania zaplecza do usługi analizy.

### <a name="using-an-alternate-http-library"></a>Korzystanie z alternatywnej biblioteki HTTP

Wywołaj metodę `.setAndroidHttpClientFactory()` natychmiast po utworzeniu odwołania do klienta.  Na przykład, aby ustawić limit czasu połączenia na 60 sekund (zamiast domyślnie 10 sekund):

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

### <a name="implement-a-progress-filter"></a>Zaimplementuj filtr postępu

Można zaimplementować przechwycenie każdego żądania, implementując `ServiceFilter`.  Na przykład następujące aktualizacje są wstępnie utworzonym paskiem postępu:

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

### <a name="customize-request-headers"></a>Dostosuj nagłówki żądań

Użyj poniższego `ServiceFilter` i Dołącz filtr w taki sam sposób jak `ProgressFilter`:

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

### <a name="conversions"></a>Konfigurowanie automatycznej serializacji

Można określić strategię konwersji, która ma zastosowanie do każdej kolumny przy użyciu interfejsu API [gson][3] . Biblioteka klienta systemu Android używa [gson][3] w tle do serializacji obiektów Java do danych JSON przed wysłaniem danych do Azure App Service.  Poniższy kod używa metody **setFieldNamingStrategy ()** do ustawiania strategii. Ten przykład usunie znak początkowy ("m"), a następnie małe litery dla każdej nazwy pola. Na przykład zmienimy wartość "mId" na "ID".  Zaimplementuj strategię konwersji, aby zmniejszyć potrzebę `SerializedName()` adnotacji w większości pól.

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

Przed utworzeniem odwołania klienta mobilnego za pomocą **MobileServiceClient**należy wykonać ten kod.

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
