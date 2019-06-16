---
title: Używana jest udostępniona sygnatur dostępu (SAS) w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, można delegować dostępu do zasobów usługi Azure Storage, w tym obiektów blob, kolejek, tabel i plików za pomocą sygnatury dostępu współdzielonego (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/18/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 8bee0426f171b0fdb7793d18c352649928fdb2e8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65907176"
---
# <a name="using-shared-access-signatures-sas"></a>Używanie sygnatur dostępu współdzielonego (SAS)

Sygnatury dostępu współdzielonego (SAS) umożliwia przyznawanie ograniczonego dostępu do obiektów na koncie magazynu innym klientom bez narażania klucz konta. W tym artykule firma Microsoft udostępnia przegląd modelu sygnatur dostępu Współdzielonego, przeglądanie najlepszych rozwiązań sygnatury dostępu Współdzielonego i Przyjrzyjmy się kilku przykładom.

Przykłady dodatkowego kodu poza tymi przedstawionych w tym miejscu przy użyciu sygnatury dostępu Współdzielonego, zobacz [wprowadzenie do usługi Azure Blob Storage na platformie .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) i inne przykłady, które są dostępne w [przykłady kodu platformy Azure](https://azure.microsoft.com/documentation/samples/?service=storage) biblioteki. Możesz Pobierz przykładowe aplikacje i uruchamiaj je lub przeglądania kodu w serwisie GitHub.

## <a name="what-is-a-shared-access-signature"></a>Co to jest sygnatura dostępu współdzielonego?
Sygnatury dostępu współdzielonego zapewnia delegowany dostęp do zasobów na koncie magazynu. Za pomocą sygnatury dostępu Współdzielonego można przyznać klientom dostęp do zasobów w ramach konta magazynu bez udostępniania kluczy konta. Jest to najbardziej istotna kwestia związana z używaniem sygnatur dostępu współdzielonego w aplikacjach — są one bezpiecznym sposobem udostępniania zasobów magazynu bez narażania kluczy konta.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Sygnatury dostępu Współdzielonego zapewnia precyzyjną kontrolę nad typu dostępu, których możesz udzielić klienci, którzy mają SAS, w tym:

* Interwał, przez który sygnatura dostępu Współdzielonego jest prawidłowy, tym czas rozpoczęcia i czas wygaśnięcia.
* Uprawnienia przyznane przez sygnatury dostępu Współdzielonego. Na przykład sygnatury dostępu Współdzielonego dla obiektu blob może udzielić odczytu i uprawnień do zapisu do tego obiektu blob, ale nie usunąć uprawnienia.
* Opcjonalny adres IP lub zakres adresów IP, z których usługi Azure Storage będzie akceptować sygnatury dostępu Współdzielonego. Na przykład można określić zakres adresów IP należących do organizacji.
* Protokół, nad którym usługi Azure Storage będzie akceptować sygnatury dostępu Współdzielonego. Ten parametr opcjonalny można użyć, aby ograniczyć dostęp do klientów przy użyciu protokołu HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Kiedy należy używać sygnatury dostępu współdzielonego?
Sygnatury dostępu Współdzielonego można użyć zapewnić dostęp do zasobów w ramach konta magazynu do każdego klienta, nie posiadającą klucze dostępu konta magazynu. Konto magazynu zawiera zarówno klucz dostępu podstawowego i pomocniczego, które udzielić dostępu administracyjnego do swojego konta i wszystkie zasoby w niej. Udostępnianie jednej z tych kluczy zostanie otwarty na możliwość użycia złośliwych lub wynikającego z niedbałości na koncie. Sygnatury dostępu współdzielonego zapewniają bezpieczne alternatywę umożliwiającą klientom odczytu, zapisu i usuwania danych w ramach konta magazynu zgodnie z uprawnieniami, które zostały jawnie przyznane uprawnienia, a także bez konieczności korzystania z klucza konta usługi.

Typowy scenariusz, w których jest użyteczny sygnatury dostępu Współdzielonego jest usługa, gdzie użytkownicy odczytu i zapisu swoje dane do swojego konta magazynu. W scenariuszu, gdzie są przechowywane dane użytkownika na koncie magazynu istnieją dwa wzorce projektów:

1. Klienci, przekazywanie i pobieranie danych za pośrednictwem usługi Serwer proxy frontonu, który przeprowadza uwierzytelnianie. Ta usługa frontonu proxy ma możliwość weryfikacji reguł biznesowych, ale w przypadku dużych ilości danych lub dużej liczby transakcji, tworzenie usługi, które można skalować w celu dopasowania żądanie może być kosztowne i trudne.

   ![Diagram scenariusza: Usługa frontonu serwera proxy](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Usługi LDS uwierzytelnia klienta, zgodnie z potrzebami, a następnie generuje sygnaturę dostępu Współdzielonego. Gdy klient odbierze sygnatury dostępu Współdzielonego, ich dostęp do zasobów konta magazynu bezpośrednio z uprawnienia zdefiniowane przez sygnatury dostępu Współdzielonego i interwał dozwolone przez sygnatury dostępu Współdzielonego. Sygnatura dostępu Współdzielonego zmniejsza potrzebę routingu wszystkich danych za pośrednictwem usługi Serwer proxy frontonu.

   ![Diagram scenariusza: Sygnatury dostępu Współdzielonego usługi dostawcy](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Wiele usług w rzeczywistych warunkach może użyć hybrydowego rozwiązania łączącego dwóm metodom. Na przykład niektóre dane mogą przetwarzane i zweryfikować za pomocą frontonu serwera proxy, podczas gdy inne dane zapisane lub odczytać bezpośrednio za pomocą sygnatury dostępu Współdzielonego.

Ponadto należy używać sygnatury dostępu Współdzielonego do autoryzowania dostępu do obiektu źródłowego w ramach operacji kopiowania, w niektórych scenariuszach:

* Podczas kopiowania obiektu blob do innego obiektu blob, które znajdują się na inne konto magazynu, należy użyć sygnatury dostępu Współdzielonego do autoryzacji dostępu do źródłowego obiektu blob. Autoryzowanie dostępu do docelowego obiektu blob oraz opcjonalnie służy sygnatury dostępu Współdzielonego.
* Podczas kopiowania pliku do innego pliku, które znajdują się na inne konto magazynu, należy użyć sygnatury dostępu Współdzielonego do autoryzowania dostępu do pliku źródłowego. Sygnatury dostępu Współdzielonego można opcjonalnie użyć do autoryzowania dostępu do tego pliku docelowego.
* Podczas kopiowania obiektu blob do pliku lub pliku do obiektu blob, należy użyć sygnatury dostępu Współdzielonego do autoryzacji dostępu do obiektu źródłowego, nawet jeśli obiekty źródłowy i docelowy znajdują się w obrębie tego samego konta magazynu.

## <a name="types-of-shared-access-signatures"></a>Rodzaje sygnatur dostępu współdzielonego
Można utworzyć dwa rodzaje sygnatur dostępu współdzielonego:

* **Sygnatura dostępu Współdzielonego usługi.** Sygnatura dostępu współdzielonego usługi deleguje dostęp do zasobu tylko w jednej z usług: obiektów blob, kolejek, tabel lub plików. Zobacz [konstruowanie sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140255.aspx) i [przykłady sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140256.aspx) Aby uzyskać szczegółowe informacje na temat tworzenia tokenu sygnatury dostępu Współdzielonego usługi.
* **Sygnatura dostępu Współdzielonego konta.** Konto sygnatury dostępu Współdzielonego obiektów delegowanych dostęp do zasobów w co najmniej jednej usługi magazynu. Wszystkie operacje, które są dostępne za pośrednictwem sygnatury dostępu Współdzielonego usługi są również dostępne za pomocą sygnatury dostępu Współdzielonego konta. Ponadto za pomocą sygnatury dostępu Współdzielonego konta, możesz delegować dostęp do operacji, które są stosowane dla danej usługi, takie jak **pobierania/ustawiania właściwości usługi** i **uzyskać statystyki usługi**. Możesz również delegować dostęp do operacji odczytu, zapisu i usuwania dla kontenerów obiektów blob, tabel, kolejek i udziałów plików, co jest niedozwolone w wypadku sygnatury dostępu współdzielonego usługi. Zobacz [konstruowanie sygnatury dostępu Współdzielonego konta](https://msdn.microsoft.com/library/mt584140.aspx) Aby uzyskać szczegółowe informacje na temat tworzenia tokenu sygnatury dostępu Współdzielonego konta.

## <a name="how-a-shared-access-signature-works"></a>Jak działa sygnatury dostępu współdzielonego
Sygnatury dostępu współdzielonego jest podpisem identyfikator URI, który wskazuje co najmniej jeden zasób magazynu i zawiera token, który zawiera specjalnego zestawu parametrów zapytania. Token wskazuje, jak zasoby można uzyskać dostęp przez klienta. Jeden z parametrów zapytania, podpis, jest zbudowany z parametrami sygnatury dostępu Współdzielonego i podpisany przy użyciu klucza konta. Podpis ten jest używany przez usługę Azure Storage do autoryzowania dostępu do zasobu magazynu.

Oto przykład identyfikatora URI sygnatury dostępu Współdzielonego, przedstawiający identyfikator URI zasobu i tokenu sygnatury dostępu Współdzielonego:

![Składniki identyfikatora URI sygnatury dostępu Współdzielonego](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

Token sygnatury dostępu Współdzielonego to ciąg, możesz wygenerować na *klienta* po stronie (zobacz [przykłady sygnatury dostępu Współdzielonego](#sas-examples) sekcji, aby uzyskać przykłady kodu). Token sygnatury dostępu Współdzielonego, który możesz wygenerować za pomocą biblioteki klienta usługi storage, na przykład nie jest śledzone przez usługę Azure Storage w dowolny sposób. Po stronie klienta, można utworzyć nieograniczoną liczbę tokenów sygnatur dostępu Współdzielonego.

Gdy klient poda identyfikator URI sygnatury dostępu Współdzielonego do usługi Azure Storage jako część żądania, usługa sprawdza parametry sygnatury dostępu Współdzielonego i podpisu, aby sprawdzić, czy jest on prawidłowy dla żądania uwierzytelniania. Jeśli usługa sprawdza, czy podpis jest prawidłowy, a następnie żądanie jest autoryzowane. W przeciwnym razie żądanie zostało odrzucone, kod błędu 403 (zabronione).

## <a name="shared-access-signature-parameters"></a>Parametry sygnatury dostępu współdzielonego
Sygnatura dostępu Współdzielonego konta i tokeny sygnatur dostępu Współdzielonego usługi obejmują niektórych wspólnych parametrów i skorzystać z kilku parametrów, które różnią się.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametry wspólne dla sygnatury dostępu Współdzielonego konta i tokeny sygnatur dostępu Współdzielonego usługi
* **Wersja interfejsu API** opcjonalny parametr określający używaną wersję usługi magazynu do użycia w celu wykonania żądania.
* **Wersja usługi** wymaganym parametrem, który określa wersję usługi magazynu do użycia w celu autoryzowania żądania.
* **Czas rozpoczęcia.** Jest to czas, w którym sygnatury dostępu Współdzielonego będzie ważna. Czas rozpoczęcia sygnatury dostępu współdzielonego jest opcjonalny. W przypadku pominięcia czas rozpoczęcia sygnatury dostępu Współdzielonego, zacznie ona obowiązywać natychmiast. Czas rozpoczęcia muszą być wyrażone w formacie UTC (Coordinated Universal Time) przy użyciu specjalnych oznaczenie UTC ("Z"), na przykład `1994-11-05T13:15:30Z`.
* **Czas wygaśnięcia.** Jest to czas, po której sygnatura dostępu Współdzielonego nie jest już prawidłowy. Najlepszym rozwiązaniem jest określenie czasu wygaśnięcia dla sygnatury dostępu Współdzielonego lub skojarzyć go z przechowywanych zasad dostępu. Czas wygaśnięcia muszą być wyrażone w formacie UTC (Coordinated Universal Time) przy użyciu specjalnych oznaczenie UTC ("Z"), na przykład `1994-11-05T13:15:30Z` (zobacz poniżej).
* **Uprawnienia.** Uprawnienia określone w sygnatury dostępu Współdzielonego wskazują, jakie operacje klienta można wykonać względem zasobu magazynu przy użyciu sygnatury dostępu Współdzielonego. Dostępne uprawnienia różnią się dla sygnatury dostępu Współdzielonego konta i sygnatury dostępu Współdzielonego usługi.
* **IP.** Opcjonalny parametr określający adres IP lub zakres adresów IP spoza platformy Azure (zobacz sekcję [stan konfiguracji sesji Routing](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) dla Express Route) z których należy akceptować żądania.
* **Protokół.** Opcjonalny parametr, który określa protokół dozwolone dla żądań. Możliwe wartości to HTTP i HTTPS (`https,http`), czyli tylko wartości domyślnej lub HTTPS (`https`). Należy pamiętać, że protokół HTTP tylko nie jest dozwoloną wartość.
* **Podpis.** Podpis jest tworzona z innych parametrów, określony jako część tokenu, a następnie szyfrowany. Podpis jest używany do autoryzowania dostępu do zasobów magazynu określony.

### <a name="parameters-for-a-service-sas-token"></a>Parametry dla tokenu sygnatury dostępu Współdzielonego usługi
* **Zasób magazynu.** Zasoby magazynu, dla których można delegować dostępu za pomocą usługi sygnatury dostępu Współdzielonego obejmują:
  * Kontenery i obiekty BLOB
  * Udziałami plików i plikami
  * Kolejki
  * Tabele i zakresy jednostki z tabeli.

### <a name="parameters-for-an-account-sas-token"></a>Parametry dla tokenu sygnatury dostępu Współdzielonego konta
* **Usługi lub usług.** Sygnatura dostępu Współdzielonego konta może delegować dostęp do co najmniej jedną z usług magazynu. Na przykład można utworzyć sygnatury dostępu Współdzielonego konta służącą do delegowania dostępu do usługi obiektów Blob i plików. Lub można utworzyć sygnaturę dostępu Współdzielonego, że delegaty dostęp do wszystkich czterech usług (obiektu Blob, kolejki, tabela i plik).
* **Typy zasobów magazynu.** Konto sygnatury dostępu Współdzielonego dotyczy co najmniej jedną klasę zasobów magazynu, a nie konkretnego zasobu. Można utworzyć sygnatury dostępu Współdzielonego, można delegować dostępu do konta:
  * Poziom usług interfejsów API, są one nazywane względem zasobu konta magazynu. Przykłady obejmują **pobierania/ustawiania właściwości usługi**, **uzyskać statystyki usługi**, i **listy kontenerów/kolejki/tabel/udziałów**.
  * Interfejsy API kontenera niskiego poziomu, która jest wywoływana względem obiektów kontenera dla każdej usługi: obiektów blob, kontenerów, kolejek, tabel i udziały plików. Przykłady obejmują **tworzenia/usuwania kontenera**, **kolejki tworzenia/usuwania**, **tworzenia/usuwania tabeli**, **udziału tworzenia/usuwania**i  **Wyświetl listę obiektów blob/plików i katalogów**.
  * Na poziomie obiektu interfejsy API, która jest wywoływana względem obiektów blob, kolejki komunikatów, jednostek tablic i plików. Na przykład **umieszczanie obiektu Blob**, **jednostka zapytania**, **pobierania komunikatów**, i **instrukcja Create File**.

## <a name="examples-of-sas-uris"></a>Przykłady identyfikatorów URI sygnatury dostępu Współdzielonego

### <a name="service-sas-uri-example"></a>Przykład identyfikatora URI sygnatury dostępu Współdzielonego usługi

Oto przykład usługi, identyfikator URI sygnatury dostępu Współdzielonego, który zawiera uprawnienia odczytu i zapisu do obiektu blob. Tabela dzieli każdą część identyfikatora URI, aby zrozumieć, jak przyczynia się do sygnatury dostępu Współdzielonego:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Name (Nazwa) | Część sygnatury dostępu Współdzielonego | Opis |
| --- | --- | --- |
| Identyfikator URI obiektu blob |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adres obiektu blob. Należy pamiętać, że przy użyciu protokołu HTTPS jest zdecydowanie zaleca się. |
| Wersja usługi magazynu |`sv=2015-04-05` |Dla magazynu usług w wersji 2012-02-12, a później, ten parametr wskazuje wersję do użycia. |
| Godzina rozpoczęcia |`st=2015-04-29T22%3A18%3A26Z` |Określona w czasie UTC. Jeśli chcesz, aby sygnatury dostępu Współdzielonego mają obowiązywać natychmiast, należy pominąć czas rozpoczęcia. |
| Czas wygaśnięcia |`se=2015-04-30T02%3A23%3A26Z` |Określona w czasie UTC. |
| Resource |`sr=b` |Zasób jest obiekt blob. |
| Uprawnienia |`sp=rw` |Uprawnienia przyznane przez sygnatury dostępu Współdzielonego obejmują Read (r) i zapisu (w). |
| Zakres adresów IP |`sip=168.1.5.60-168.1.5.70` |Zakres adresów IP, z których będą akceptowane żądania. |
| Protocol |`spr=https` |Dozwolone są tylko żądania przy użyciu protokołu HTTPS. |
| Podpis |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Umożliwia autoryzację dostępu do obiektu blob. Podpis jest HMAC obliczona logowania do ciągów i klucza przy użyciu algorytm SHA256, a następnie kodowany w formacie Base64. |

### <a name="account-sas-uri-example"></a>Przykład użycia identyfikatora URI sygnatury dostępu Współdzielonego konta

Oto przykład używający tego samego wspólne parametry w tokenie sygnatury dostępu Współdzielonego konta. Ponieważ te parametry są opisane powyżej, ich nie zostały opisane w tym miejscu. Tylko parametry, które są specyficzne dla konta, którego sygnatury dostępu Współdzielonego są opisane w poniższej tabeli.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Name (Nazwa) | Część sygnatury dostępu Współdzielonego | Opis |
| --- | --- | --- |
| Identyfikator URI zasobu |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Punkt końcowy usługi Blob, za pomocą parametrów dla pobierania właściwości usługi (jeśli jest to nazywane za pomocą GET) lub właściwości usługi (jeśli jest to nazywane z ZESTAWEM). |
| Usługi |`ss=bf` |Sygnatura dostępu Współdzielonego ma zastosowanie do usług obiektów Blob i plików |
| Typy zasobów |`srt=s` |Sygnatura dostępu Współdzielonego dotyczy operacji poziomu usług. |
| Uprawnienia |`sp=rw` |Uprawnienia udzielić dostępu do operacji odczytu i zapisu. |

Biorąc pod uwagę, że uprawnienia są ograniczone do poziomu usługi, są dostępne operacje przy użyciu tej sygnatury dostępu Współdzielonego **pobrać właściwości usługi obiektów Blob** (odczyt) i **ustawiania właściwości usługi obiektów Blob** (zapis). Jednak z zasobem inny identyfikator URI tego samego tokenu sygnatury dostępu Współdzielonego można również delegować dostęp do **uzyskać statystyki usługi obiektów Blob** (odczyt).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Kontrolowanie sygnatury dostępu Współdzielonego przy użyciu przechowywanych zasad dostępu
Sygnatury dostępu współdzielonego można wykonać jedną z dwóch formach:

* **Skojarzenia zabezpieczeń ad hoc:** Podczas tworzenia sygnatury dostępu Współdzielonego ad-hoc, czas rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego są wszystkie określone w identyfikatorze URI sygnatury dostępu Współdzielonego (lub domniemanych, w przypadku, gdy czas rozpoczęcia jest pominięty). Ten typ sygnatury dostępu Współdzielonego można utworzyć sygnatury dostępu Współdzielonego konta lub sygnatury dostępu Współdzielonego usługi.
* **Sygnatury dostępu Współdzielonego przy użyciu przechowywanych zasad dostępu:** Przechowywanych zasad dostępu jest zdefiniowany w kontenerze zasobów — kontener obiektów blob, tabeli, kolejki, lub udziału--plików i może służyć do zarządzania ograniczeniami dla jednego lub więcej sygnatury dostępu współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest kojarzony z przechowywanych zasad dostępu, sygnatury dostępu Współdzielonego dziedziczy ograniczeń — godzina rozpoczęcia, czas wygaśnięcia i uprawnienia — zdefiniowane dla przechowywanych zasad dostępu.

> [!NOTE]
> Obecnie sygnatury dostępu Współdzielonego konta musi być ad-hoc sygnatury dostępu Współdzielonego. Przechowywane dostępu, zasady nie są jeszcze obsługiwane dla sygnatury dostępu Współdzielonego konta.

Różnica między dwoma formami jest ważna dla jednego kluczowy scenariusz: odwołania. Ponieważ identyfikator URI sygnatury dostępu Współdzielonego jest adres URL, każdy użytkownik, który uzyskuje sygnaturę dostępu Współdzielonego można użyć, niezależnie od tego, który został pierwotnie utworzony. Sygnatury dostępu Współdzielonego zostanie opublikowany publicznie, może służyć przez dowolną osobę na całym świecie. Sygnatury dostępu Współdzielonego udziela dostępu do zasobów dla każdego, kto czym go, dopóki jedna z następujących czterech zdarzeń sytuacji:

1. Czas wygaśnięcia, określone w sygnatury dostępu Współdzielonego zostanie osiągnięty.
2. Czas wygaśnięcia, określone w zasadach dostępu składowana odwołuje się sygnatury dostępu Współdzielonego zostanie osiągnięty (jeśli jest wywoływany przechowywanych zasad dostępu i określa czas wygaśnięcia). Taka sytuacja może wystąpić, ponieważ on upłynie lub został zmodyfikowany przechowywanych zasad dostępu za pomocą czas wygaśnięcia w przeszłości, który jest jednym ze sposobów, aby można było odwołać sygnatury dostępu Współdzielonego.
3. Usunąć przechowywanych zasad dostępu odwołuje się sygnatury dostępu Współdzielonego, który jest inny sposób, aby można było odwołać sygnatury dostępu Współdzielonego. Należy pamiętać, że jeśli ponownie przechowywane zasady dostępu o dokładnie takiej samej nazwie, wszystkie istniejące tokeny sygnatur dostępu Współdzielonego ponownie będzie nieprawidłowa uprawnienia skojarzone z tym przechowywanych zasad dostępu (przy założeniu, że który nie został przekazany czas wygaśnięcia na sygnatury dostępu Współdzielonego). Mają zamiar odwołać sygnatury dostępu Współdzielonego, należy użyć innej nazwy, jeśli ponownie zasady dostępu z czasem wygaśnięcia w przyszłości.
4. Klucz konta, który został użyty do utworzenia sygnatury dostępu Współdzielonego zostanie ponownie wygenerowany. Trwa ponowne generowanie klucza konta spowoduje, że wszystkie składniki aplikacji nie można autoryzować aż te są aktualizowane klucz prawidłowego konta lub konta nowo wygenerowano ponownie klucz za pomocą tego klucza.

> [!IMPORTANT]
> Identyfikator URI sygnatury dostępu współdzielonego jest skojarzony z kluczem konta używane do tworzenia podpisu, a skojarzone przechowywane zasady dostępu (jeśli istnieje). Jeśli nie przechowywanych zasad dostępu jest określona, jedynym sposobem, aby można było odwołać sygnatury dostępu współdzielonego jest należy zmienić wartość klucza konta.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Uwierzytelniania na podstawie aplikacji klienckiej, za pomocą sygnatury dostępu Współdzielonego
Klient, który jest w posiadaniu sygnatury dostępu Współdzielonego można używać sygnatury dostępu Współdzielonego do autoryzowania żądania względem konta magazynu, dla której nie posiadają kluczy konta. Sygnatury dostępu Współdzielonego mogą być zawarte w parametrach połączenia lub używane bezpośrednio z odpowiedniego konstruktora lub metody.

### <a name="using-a-sas-in-a-connection-string"></a>W parametrach połączenia przy użyciu sygnatury dostępu Współdzielonego
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Przy użyciu sygnatury dostępu Współdzielonego w konstruktorze lub — metoda
Kilka konstruktorów biblioteki klienta usługi Azure Storage i przeciążenia metody oferują parametrem sygnatury dostępu Współdzielonego, dzięki czemu można autoryzować żądania do usługi przy użyciu sygnatury dostępu Współdzielonego.

Na przykład identyfikatora URI sygnatury dostępu Współdzielonego jest tu używany do utworzenia odwołanie do blokowego obiektu blob. Sygnatura dostępu Współdzielonego zapewnia tylko poświadczenia wymagane dla żądania. Odwołanie do obiektu blob bloku jest następnie używany dla operacji zapisu:

```csharp
string sasUri = "https://storagesample.blob.core.windows.net/sample-container/" +
    "sampleBlob.txt?sv=2015-07-08&sr=b&sig=39Up9JzHkxhUIhFEjEH9594DJxe7w6cIRCg0V6lCGSo%3D" +
    "&se=2016-10-18T21%3A51%3A37Z&sp=rcw";

CloudBlockBlob blob = new CloudBlockBlob(new Uri(sasUri));

// Create operation: Upload a blob with the specified name to the container.
// If the blob does not exist, it will be created. If it does exist, it will be overwritten.
try
{
    MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    msWrite.Position = 0;
    using (msWrite)
    {
        await blob.UploadFromStreamAsync(msWrite);
    }

    Console.WriteLine("Create operation succeeded for SAS {0}", sasUri);
    Console.WriteLine();
}
catch (StorageException e)
{
    if (e.RequestInformation.HttpStatusCode == 403)
    {
        Console.WriteLine("Create operation failed for SAS {0}", sasUri);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    else
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}

```

## <a name="best-practices-when-using-sas"></a>Najlepsze rozwiązania przy użyciu sygnatury dostępu Współdzielonego
Korzystając z sygnatury dostępu współdzielonego w aplikacjach, musisz znać dwie potencjalne zagrożenia:

* Przeciek sygnatury dostępu Współdzielonego może służyć każda osoba, która uzyskuje, w którym potencjalnie może naruszyć bezpieczeństwo konta magazynu.
* Jeśli sygnatury dostępu Współdzielonego przekazana aplikacja klienta wygaśnie aplikacji nie może pobrać nowe sygnatury dostępu Współdzielonego z usługi, a następnie mogą napotykać funkcjonalności aplikacji.

Poniższe zalecenia dotyczące używania sygnatur dostępu współdzielonego mogą pomóc ograniczyć te zagrożenia:

1. **Zawsze używaj protokołu HTTPS** dotyczące tworzenia i rozpowszechniania sygnatury dostępu Współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest przekazywany za pośrednictwem protokołu HTTP, a przechwycone, osoba przeprowadzająca atak typu man-in--middle będzie mógł odczytać sygnatury dostępu Współdzielonego, a następnie użyć go, zgodnie z właściwym użytkownikiem może mieć potencjalne zagrożenia dla poufnych danych lub zezwalanie na uszkodzenie danych przez złośliwego użytkownika.
2. **Odwołanie przechowywane zasady dostępu, jeśli jest to możliwe.** Zapisane zasady dostępu zapewniają możliwość odwołać uprawnienia bez konieczności ponownego generowania kluczy konta magazynu. Ustaw czas wygaśnięcia na ten temat bardzo daleko w przyszłości (lub nieskończona) i upewnij się, że jest regularnie aktualizowana, aby przenieść go dalej w przyszłości.
3. **Użyj najbliższej czas wygaśnięcia na ad-hoc sygnatury dostępu Współdzielonego.** W ten sposób nawet wtedy, gdy sygnatury dostępu Współdzielonego zostanie naruszony, jest on prawidłowy tylko przez krótki czas. Tej praktyką jest szczególnie ważne, jeśli nie mogą odwoływać się przechowywanych zasad dostępu. Czas wygaśnięcia najbliższych planach także ograniczać ilość danych, które mogą być zapisywane do obiektu blob, ograniczając czas można przekazać do niego.
4. **Mieć klientów automatycznie odnawiane sygnatury dostępu Współdzielonego, jeśli to konieczne.** Klienci powinni odnawiać sygnatury dostępu Współdzielonego przed wygaśnięciem, aby umożliwić czasu ponownych prób, jeśli usługa oferująca sygnatury dostępu Współdzielonego jest niedostępny. Jeśli Twoje sygnatury dostępu Współdzielonego jest przeznaczona do użytku z małą liczbą natychmiastowy, krótkotrwałych operacje, które powinny zostać wykonane w okresie ważności, następnie może to być konieczne, gdyż nie oczekiwano sygnatury dostępu Współdzielonego do odnowienia. Jednak jeśli masz klienta, który regularnie zgłasza żądania za pomocą sygnatury dostępu Współdzielonego, następnie możliwości wygaśnięcia trafia do odtwarzania. Kluczowe zagadnienia to zrównoważyć potrzebę być krótkotrwały sygnatur dostępu Współdzielonego (jak wcześniej wspomniano) konieczności upewnij się, że klient żąda odnowienia wcześnie wystarczającej ilości (w celu uniknięcia przerw w działaniu z powodu sygnatury dostępu Współdzielonego wygaśnie przed pomyślnym odnawiania).
5. **Należy być ostrożnym z czas rozpoczęcia sygnatury dostępu Współdzielonego.** Możesz ustawić czas rozpoczęcia sygnatur dostępu współdzielonego **teraz**, następnie ze względu na zegarze pochylanie (różnice w bieżącej godziny zgodnie z różnych maszyn), błędy można zaobserwować sporadycznie za pierwsze kilka minut. Ogólnie rzecz biorąc należy ustawić czas rozpoczęcia to co najmniej 15 minut w przeszłości. Lub nie ustawisz go, który spowoduje, że prawidłowy natychmiast we wszystkich przypadkach. Zwykle dotyczy to również — czas wygaśnięcia należy pamiętać, że można zaobserwować do 15 minut zegara niesymetryczność w dowolnym kierunku na każde żądanie. Dla klientów używających REST w wersji wcześniejszej niż 2012-02-12 maksymalny czas trwania sygnatury dostępu Współdzielonego, który nie odwołuje się przechowywanych zasad dostępu to 1 godzina, a wszystkie zasady, określając dłuższym okresie nie powiedzie się.
6. **Być określony z zasobem, który ma być uzyskiwany dostęp.** Najbezpieczniejszym rozwiązaniem jest zapewnienie użytkownika minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko do odczytu w pojedynczą jednostkę, następnie przyznać im dostęp do odczytu do tego pojedynczej jednostki, a nie odczytu/zapisu/usuwania dostęp do wszystkich jednostek. Pomaga to również zmniejszyć szkody, jeśli sygnatury dostępu Współdzielonego zostanie naruszony, ponieważ sygnatura dostępu Współdzielonego ma mniej energii w ręce osoba atakująca.
7. **Dowiedz się, że Twoje konto zostanie naliczona za każde użycie, w tym zrobić za pomocą sygnatury dostępu Współdzielonego.** Jeśli podasz dostęp do zapisu do obiektu blob, użytkownik może zdecydować się na przekażesz obiekt blob 200GB. Jeśli zostało podane je także dostęp do odczytu, mogą one jego pobranie 10 razy ponoszenia 2 TB w koszty ruchu wychodzącego dla Ciebie. Ponownie podaj ograniczonymi uprawnieniami, aby ułatwić uniknięcie potencjalnych akcji złośliwych użytkowników. Użyj krótkotrwałe sygnatury dostępu Współdzielonego, aby zmniejszyć to zagrożenie (lecz można je na uwadze zegara na czas zakończenia).
8. **Sprawdzanie poprawności danych zapisanych przy użyciu sygnatury dostępu Współdzielonego.** Gdy aplikacja kliencka zapisuje dane do swojego konta magazynu, należy pamiętać o tym, że mogą wystąpić problemy z tymi danymi. Jeśli aplikacja wymaga, że dane można sprawdzić poprawności lub autoryzowane, zanim będzie gotowa do użycia, należy to wykonać tej weryfikacji po zapisaniu danych oraz zanim zostanie on użyty przez aplikację. To rozwiązanie chroni także uszkodzony lub złośliwego danych zapisywanych do konta przez użytkownika, który prawidłowo pobrać sygnatury dostępu Współdzielonego lub przez użytkownika, wykorzystaniu ujawnione sygnatury dostępu Współdzielonego.
9. **Nie należy zawsze używać sygnatury dostępu Współdzielonego.** Czasami ryzyko związane z daną operację względem konta usługi storage przeważyć zalety sygnatury dostępu Współdzielonego. Dla takich działań, Utwórz usługa warstwy środkowej, która zapisuje do swojego konta magazynu po wykonaniu firm reguły sprawdzania poprawności, uwierzytelnianie i inspekcji. Czasami jest także łatwiej zarządzać dostępem w inny sposób. Na przykład, chcąc odczytywać wszystkie obiekty BLOB w kontenerze publicznie, możesz ustawić kontener Public, zamiast udostępniać sygnatury dostępu Współdzielonego do każdego klienta, aby uzyskać dostęp.
10. **Usługa Storage Analytics umożliwia monitorowanie aplikacji.** Rejestrowanie i metryki służy do przestrzegania wszelkich wzrost niepowodzeń uwierzytelniania z powodu awarii w usłudze dostawcy sygnatury dostępu Współdzielonego lub nieumyślne usunięcie przechowywanych zasad dostępu. Zobacz [Blog zespołu usługi Azure Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) Aby uzyskać dodatkowe informacje.

## <a name="sas-examples"></a>Przykłady sygnatury dostępu Współdzielonego
Poniżej przedstawiono kilka przykładów oba rodzaje sygnatur dostępu współdzielonego, sygnatury dostępu Współdzielonego konta i usługi.

Aby uruchomić te przykłady języka C#, należy odwoływać się do następujących pakietów NuGet w projekcie:

* [Biblioteka klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage), w wersji 6.x lub nowszej (, aby użyć konta sygnatury dostępu Współdzielonego).
* [Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager)

Aby uzyskać więcej przykładów, które pokazują, jak tworzyć i testować sygnatury dostępu Współdzielonego, zobacz [przykłady kodu platformy Azure do przechowywania](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Przykład: Tworzenie i używanie sygnatury dostępu Współdzielonego konta
Poniższy przykład kodu tworzy konto sygnatury dostępu Współdzielonego, który nadaje się do usług obiektów Blob i plików i daje klientowi uprawnień odczytu, zapisu i uprawnienia listy dostęp do interfejsów API z poziomu usługi. Sygnatura dostępu Współdzielonego konta ogranicza protokołu HTTPS, dlatego żądanie musi zostać wykonane przy użyciu protokołu HTTPS.

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use your shared key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

Aby użyć konta sygnatury dostępu Współdzielonego, dostęp do interfejsów API poziomu usług, dla usługi Blob, skonstruować obiekt klienta obiektu Blob przy użyciu sygnatury dostępu Współdzielonego i punktu końcowego magazynu obiektów Blob dla konta magazynu.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "account-name", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

### <a name="example-create-a-stored-access-policy"></a>Przykład: Utwórz przechowywanych zasad dostępu
Poniższy kod tworzy przechowywanych zasad dostępu do kontenera. Aby określić ograniczenia dla sygnatury dostępu Współdzielonego usługi kontenera lub jego obiektów blob, można użyć zasad dostępu.

```csharp
private static async Task CreateSharedAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new shared access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
        // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

### <a name="example-create-a-service-sas-on-a-container"></a>Przykład: Tworzenie sygnatury dostępu Współdzielonego usługi do kontenera
Poniższy kod tworzy sygnatury dostępu Współdzielonego w kontenerze. Jeśli podano nazwę istniejącej zasady dostępu przechowywane tej zasady jest skojarzony z sygnatury dostępu Współdzielonego. Jeśli nie podano żadnych przechowywanych zasad dostępu, następnie kod tworzy ad-hoc sygnatury dostępu Współdzielonego dla kontenera.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container, setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case, all of the constraints for the
        // shared access signature are specified on the stored access policy, which is provided by name.
        // It is also possible to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Przykład: Tworzenie sygnatury dostępu Współdzielonego usługi na obiekcie blob
Poniższy kod tworzy sygnatury dostępu Współdzielonego na obiekcie blob. Jeśli podano nazwę istniejącej zasady dostępu przechowywane tej zasady jest skojarzony z sygnatury dostępu Współdzielonego. Jeśli nie podano żadnych przechowywanych zasad dostępu, następnie kod tworzy ad-hoc sygnatury dostępu Współdzielonego w obiekcie blob.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container, string blobName, string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad hoc SAS, and
        // to construct a shared access policy that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be the time when the storage service receives the request.
            // Omitting the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob, setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints for the
        // shared access signature are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

## <a name="conclusion"></a>Podsumowanie
Sygnatury dostępu współdzielonego są przydatne do prezentowania ograniczonych uprawnień do konta magazynu dla klientów, którzy nie powinni mieć klucz konta. W efekcie są to ważna część model zabezpieczeń dla każdej aplikacji za pomocą usługi Azure Storage. Jeśli stosujesz najlepsze rozwiązania wymienione w tym miejscu, można użyć sygnatury dostępu Współdzielonego, aby zapewnić większą elastyczność dostępu do zasobów w ramach konta magazynu bez uszczerbku dla zabezpieczeń aplikacji.

## <a name="next-steps"></a>Następne kroki
* [Zarządzanie dostępem anonimowym odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md)
* [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Wprowadzenie do tabel i token SAS kolejki](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
