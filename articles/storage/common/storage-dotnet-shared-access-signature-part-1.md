---
title: Używanie udostępnionego sygnatur dostępu (SAS) w usłudze Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, Użyj sygnatur dostępu współdzielonego (SAS), aby delegować dostęp do zasobów usługi Azure Storage, w tym obiekty BLOB, kolejek, tabel i plików.
services: storage
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
editor: tysonn
ms.assetid: 46fd99d7-36b3-4283-81e3-f214b29f1152
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/18/2017
ms.author: cshoe
ms.openlocfilehash: d3f8b3261f9e2e86dbcaa41b92111545abeffe54
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="using-shared-access-signatures-sas"></a>Przy użyciu sygnatury dostępu współdzielonego (SAS)

Sygnatury dostępu współdzielonego (SAS) zapewnia sposób, aby przyznać ograniczony dostęp do obiektów na koncie magazynu innym klientom bez narażania klucz konta. W tym artykule firma Microsoft Podaj omówienie modelu SAS, przejrzyj SAS najlepszych rozwiązań i przyjrzyj się przykłady.

Dla dodatkowych przykładów kodu poza tymi przedstawionych w tym miejscu przy użyciu sygnatury dostępu Współdzielonego, zobacz [wprowadzenie do magazynu obiektów Blob Azure w programie .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/) i inne przykłady, które są dostępne w [przykłady kodu Azure](https://azure.microsoft.com/documentation/samples/?service=storage) biblioteki. Możesz Pobierz przykładowe aplikacje i uruchom je lub Przeglądaj kodu w witrynie GitHub.

## <a name="what-is-a-shared-access-signature"></a>Co to jest sygnatury dostępu współdzielonego?
Sygnatury dostępu współdzielonego umożliwiają dostęp delegowany do zasobów na koncie magazynu. Z sygnatury dostępu Współdzielonego można przyznać klientom dostęp do zasobów na koncie magazynu bez udostępniania kluczy konta. Jest to najbardziej istotna kwestia związana z używaniem sygnatur dostępu współdzielonego w aplikacjach — są one bezpiecznym sposobem udostępniania zasobów magazynu bez narażania kluczy konta.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

Sygnatury dostępu Współdzielonego zapewnia kontrolę nad typ dostępu udzielić klientów, którzy mają SAS, w tym:

* Interwał, w którym jest prawidłowa, w tym czas rozpoczęcia i czas wygaśnięcia sygnatury dostępu Współdzielonego.
* Uprawnienia przyznane przez sygnatury dostępu Współdzielonego. Na przykład sygnatury dostępu Współdzielonego dla obiekt blob może udzielić odczytu i uprawnienia zapisu do tego obiektu blob, ale nie usunąć uprawnienia.
* Opcjonalne adres IP lub zakres adresów IP, z których Magazyn Azure będzie akceptować sygnatury dostępu Współdzielonego. Na przykład może określić zakres adresów IP należących do organizacji.
* Protokół, w którym magazyn Azure będzie akceptować sygnatury dostępu Współdzielonego. Ten parametr opcjonalny służy do ograniczania dostępu do klientów przy użyciu protokołu HTTPS.

## <a name="when-should-you-use-a-shared-access-signature"></a>Kiedy należy używać sygnatury dostępu współdzielonego
Aby zapewnić dostęp do zasobów na koncie magazynu dla każdego klienta, nie posiadającą kluczy dostępu do konta magazynu, można użyć sygnatury dostępu Współdzielonego. Twoje konto magazynu obejmuje zarówno klucz dostępu podstawowych i pomocniczych, które przyznawaj dostęp administracyjny do konta i wszystkie zasoby w niej. Żaden z tych kluczy udostępnianie otwiera konta możliwości złośliwego lub brak dbałości o ich użycia. Sygnatury dostępu współdzielonego zapewniają bezpieczne alternatywę umożliwiającą klientom odczytu, zapisu i usuwania danych na koncie magazynu, zgodnie z uprawnieniami, które zostały jawnie przyznane uprawnienia i bez potrzeby klucza konta.

Typowy scenariusz, w których sygnatury dostępu Współdzielonego jest użyteczny to usługa, gdzie użytkownicy odczytu i zapisu własne dane do konta magazynu. W scenariuszu, w którym konta magazynu są przechowywane dane użytkownika istnieją dwa wzorce projektowe typowe:

1. Klienci przekazywania i pobierania danych za pomocą usługi frontonu serwera proxy, który przeprowadza uwierzytelnianie. Ta usługa proxy frontonu ma możliwość sprawdzania poprawności reguł biznesowych, ale w przypadku dużych ilości danych lub dużej liczby transakcji, tworzenie usługi, które można skalować do dopasowania żądanie może być kosztowne lub trudne.

  ![Diagram scenariusza: Usługa frontonu serwera proxy](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png)   

1. Usługi LDS uwierzytelnia klientów zgodnie z potrzebami, a następnie generuje sygnaturę dostępu Współdzielonego. Gdy klient odbierze sygnatury dostępu Współdzielonego, uzyskiwania dostępu do zasobów konta magazynu bezpośrednio z na uprawnienia zdefiniowane przez sygnatury dostępu Współdzielonego i interwał dozwoloną sygnatury dostępu Współdzielonego. Sygnatury dostępu Współdzielonego zmniejsza potrzebę routingu wszystkich danych za pośrednictwem usługi Serwer proxy frontonu.

  ![Diagram scenariusza: sygnatury dostępu Współdzielonego usługi dostawcy](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png)   

Wiele usług rzeczywistych może używać hybrydowego te dwie metody. Na przykład niektóre dane może być przetwarzane i sprawdzone za pomocą frontonu serwera proxy, podczas gdy inne dane zapisane i/lub odczytać bezpośrednio przy użyciu sygnatury dostępu Współdzielonego.

Ponadto będą musieli używać sygnatury dostępu Współdzielonego do uwierzytelniania obiektu źródłowego w operacji kopiowania w niektórych scenariuszach:

* Podczas kopiowania obiektu blob do innego obiektu blob, która znajduje się w innego konta magazynu, należy użyć do uwierzytelniania źródłowego obiektu blob sygnatury dostępu Współdzielonego. Opcjonalnie można sygnatury dostępu Współdzielonego docelowego obiektu blob również uwierzytelniania.
* Podczas kopiowania pliku do innego pliku, która znajduje się w innego konta magazynu, należy użyć do uwierzytelniania w pliku źródłowym sygnatury dostępu Współdzielonego. Sygnatury dostępu Współdzielonego można opcjonalnie użyć do uwierzytelniania również plik docelowy.
* Podczas kopiowania obiektu blob do pliku lub pliku do obiektu blob, należy użyć sygnatury dostępu Współdzielonego do uwierzytelniania obiektu źródłowego, nawet jeśli obiekty źródłowy i docelowy znajdują się w obrębie tego samego konta magazynu.

## <a name="types-of-shared-access-signatures"></a>Rodzaje sygnatur dostępu współdzielonego
Można utworzyć dwa rodzaje sygnatur dostępu współdzielonego:

* **Usługa sygnatury dostępu Współdzielonego.** Sygnatura dostępu współdzielonego usługi deleguje dostęp do zasobu tylko w jednej z usług: obiektów blob, kolejek, tabel lub plików. Zobacz [konstruowania sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140255.aspx) i [przykłady sygnatury dostępu Współdzielonego usługi](https://msdn.microsoft.com/library/dn140256.aspx) Aby uzyskać szczegółowe informacje na temat tworzenia tokenu sygnatury dostępu Współdzielonego usługi.
* **Sygnatura dostępu Współdzielonego konta.** Konto sygnatury dostępu Współdzielonego delegatów dostęp do zasobów w co najmniej jednej usługi magazynu. Wszystkie operacje dostępne za pomocą sygnatury dostępu Współdzielonego usługi są również dostępne za pośrednictwem konta sygnatury dostępu Współdzielonego. Ponadto przy użyciu sygnatury dostępu Współdzielonego konta, możesz delegować dostęp do operacji, które są stosowane do danej usługi, takie jak **pobierania/ustawiania właściwości usługi** i **uzyskać statystyki usługi**. Możesz również delegować dostęp do operacji odczytu, zapisu i usuwania dla kontenerów obiektów blob, tabel, kolejek i udziałów plików, co jest niedozwolone w wypadku sygnatury dostępu współdzielonego usługi. Zobacz [konstruowania SAS konta](https://msdn.microsoft.com/library/mt584140.aspx) Aby uzyskać szczegółowe informacje na temat tworzenia tokenu sygnatury dostępu Współdzielonego konta.

## <a name="how-a-shared-access-signature-works"></a>Jak działa sygnatury dostępu współdzielonego
Sygnatury dostępu współdzielonego jest podpisem identyfikator URI, który wskazuje na jeden lub więcej zasobów magazynu i zawiera token, który zawiera specjalnego zestawu parametrów zapytania. Token wskazuje, jak zasoby mogą uzyskiwać przez klienta. Jeden z parametrów zapytania, podpisu, jest utworzone na podstawie parametrów sygnatury dostępu Współdzielonego i podpisany przy użyciu klucza konta. Ta sygnatura jest używany przez usługi Azure Storage do uwierzytelniania sygnatury dostępu Współdzielonego.

Oto przykład identyfikatora URI połączenia SAS, przedstawiający identyfikator URI zasobu i tokenu sygnatury dostępu Współdzielonego:

![Składniki identyfikatora URI sygnatury dostępu Współdzielonego](./media/storage-dotnet-shared-access-signature-part-1/sas-storage-uri.png)   

SAS token jest generowanie na ciąg *klienta* po stronie (zobacz [przykłady SAS](#sas-examples) sekcji Przykłady kodu). Token sygnatury dostępu Współdzielonego, które można wygenerować z biblioteki klienta usługi storage, na przykład nie będzie śledzona przez usługi Azure Storage w dowolny sposób. Po stronie klienta, można utworzyć nieograniczoną liczbę tokeny sygnatury dostępu Współdzielonego.

Gdy klient poda identyfikatora URI sygnatury dostępu Współdzielonego do magazynu Azure jako część żądania, usługa sprawdza SAS parametry i podpis, aby sprawdzić, czy jest nieprawidłowa dla żądania uwierzytelniania. Jeśli usługa sprawdza, czy podpis jest prawidłowy, a następnie żądanie jest uwierzytelniane. W przeciwnym razie żądanie zostało odrzucone z kodem błędu 403 (Dostęp zabroniony).

## <a name="shared-access-signature-parameters"></a>Parametry sygnatury dostępu współdzielonego
Sygnatura dostępu Współdzielonego konta i tokeny sygnatury dostępu Współdzielonego usługi obejmują niektóre typowe parametry, a także zająć kilka parametrów, które są inne.

### <a name="parameters-common-to-account-sas-and-service-sas-tokens"></a>Parametry wspólne dla konta sygnatury dostępu Współdzielonego i tokeny sygnatury dostępu Współdzielonego usługi
* **Wersja interfejsu API** opcjonalny parametr określający wersję usługi magazynu do użycia, można wykonać żądania.
* **Wersja usługi** wymaganym parametrem, który określa wersję usługi magazynu do użycia uwierzytelniania żądania.
* **Godzina rozpoczęcia.** Jest to czas, w którym zaczyna obowiązywać sygnatury dostępu Współdzielonego. Czas rozpoczęcia sygnatury dostępu współdzielonego jest opcjonalne. W przypadku pominięcia czas rozpoczęcia sygnatury dostępu Współdzielonego obowiązuje natychmiast. Czas rozpoczęcia musi być wyrażony w formacie UTC (Coordinated Universal Time) z specjalne oznaczeniem UTC ("Z"), na przykład `1994-11-05T13:15:30Z`.
* **Czas wygaśnięcia.** Jest to czas, po którym skojarzenia zabezpieczeń nie jest już prawidłowy. Najlepszym rozwiązaniem jest Określ czas wygaśnięcia dla sygnatury dostępu Współdzielonego lub powiązać ją z zasadami dostępu przechowywane. Czas wygaśnięcia musi być wyrażony w formacie UTC (Coordinated Universal Time) z specjalne oznaczeniem UTC ("Z"), na przykład `1994-11-05T13:15:30Z` (Zobacz więcej poniżej).
* **Uprawnienia.** Uprawnienia określone na sygnatury dostępu Współdzielonego wskazują, jakie operacje klienta można wykonywać względem zasobów pamięci masowej przy użyciu sygnatury dostępu Współdzielonego. Uprawnienia dostępne są różne dla konta sygnatury dostępu Współdzielonego i sygnatury dostępu Współdzielonego usługi.
* **IP.** Opcjonalny parametr określający adres IP lub zakres adresów IP poza platformą Azure (zobacz sekcję [stan konfiguracji sesji Routing](../../expressroute/expressroute-workflows.md#routing-session-configuration-state) dla Express Route) służąca do akceptowania żądań.
* **Protokół.** Opcjonalny parametr, który określa protokół, który jest dozwolony dla żądania. Możliwe wartości to zarówno protokołu HTTPS i HTTP (`https,http`), czyli tylko wartości domyślnej lub HTTPS (`https`). Należy pamiętać, że HTTP tylko nie jest dozwoloną wartość.
* **Podpis.** Podpis jest tworzony z innych parametrów, określony jako część tokenu i ponownie szyfrowane. Jest on używany do uwierzytelniania sygnatury dostępu Współdzielonego.

### <a name="parameters-for-a-service-sas-token"></a>Parametry dla tokenu sygnatury dostępu Współdzielonego usługi
* **Zasobów pamięci masowej.** Zasobów magazynu, dla których możesz delegować dostęp za pomocą usługi SAS obejmują:
  * Kontenery i obiekty BLOB
  * Udziały plików i plików
  * Kolejki
  * Tabele i zakresy jednostek do tabeli.

### <a name="parameters-for-an-account-sas-token"></a>Parametry dla tokenu sygnatury dostępu Współdzielonego konta
* **Usługi lub usług.** Sygnatura dostępu Współdzielonego konta można delegować dostęp do co najmniej jednej usługi magazynu. Na przykład można utworzyć konto sygnatury dostępu Współdzielonego czy delegatów dostęp do usług obiektów Blob i plików. Lub można utworzyć sygnatury dostępu Współdzielonego, że delegatów dostęp do wszystkich czterech usług (obiektu Blob, kolejki, tabel i plików).
* **Typy zasobów magazynu.** Konto sygnatury dostępu Współdzielonego ma zastosowanie do co najmniej jednej klasy zasobów magazynu, a nie konkretnego zasobu. Można utworzyć sygnatury dostępu Współdzielonego, aby delegować dostęp do konta:
  * API poziomu usług jest wywoływana względem zasobów konta magazynu. Przykłady obejmują **pobierania/ustawiania właściwości usługi**, **uzyskać statystyki usługi**, i **listy kontenerów/kolejki/tabel/udziałów**.
  * Interfejsy API kontenera niskiego poziomu, nazywane względem obiektów kontenera dla poszczególnych usług: obiektów blob w kontenerach, kolejek, tabel i udziałów plików. Przykłady obejmują **tworzenia/usuwania kontenera**, **tworzenia/usuwania kolejki**, **tworzenia/usuwania tabeli**, **tworzenia/usuwania udziału**i  **Wyświetl listę obiektów blob/pliki i katalogi**.
  * API na poziomie obiektu jest wywoływana względem obiektów blob, wiadomości w kolejce jednostek tabeli i plików. Na przykład **umieszczanie obiektu Blob**, **jednostki zapytania**, **pobrać wiadomości**, i **Utwórz plik**.

## <a name="examples-of-sas-uris"></a>Przykłady identyfikatorów URI sygnatury dostępu Współdzielonego

### <a name="service-sas-uri-example"></a>Przykładowy identyfikator URI sygnatury dostępu Współdzielonego usługi

Oto przykład usługi identyfikatora URI połączenia SAS, która zapewnia uprawnienia odczytu i zapisu do obiektu blob. Tabela zawiera podział według każdej części identyfikatora URI, aby zrozumieć, jak przyczynia się do sygnatury dostępu Współdzielonego:

```
https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D
```

| Name (Nazwa) | Część SAS | Opis |
| --- | --- | --- |
| Identyfikator URI obiektu blob |`https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt` |Adres obiektu blob. Należy pamiętać, że przy użyciu protokołu HTTPS jest zdecydowanie zalecane. |
| Wersja usługi magazynu |`sv=2015-04-05` |Magazyn usługi wersji 2012-02-12, a później, ten parametr wskazuje wersję do użycia. |
| Godzina rozpoczęcia |`st=2015-04-29T22%3A18%3A26Z` |Określona w formacie UTC. Jeśli chcesz, aby sygnatury dostępu Współdzielonego ma obowiązywać natychmiast, Pomiń czas rozpoczęcia. |
| Czas wygaśnięcia |`se=2015-04-30T02%3A23%3A26Z` |Określona w formacie UTC. |
| Zasób |`sr=b` |Zasób jest obiektu blob. |
| Uprawnienia |`sp=rw` |Uprawnienia przyznane przez sygnatury dostępu Współdzielonego obejmują Read (r) i zapisu (w). |
| Zakres adresów IP |`sip=168.1.5.60-168.1.5.70` |Zakres adresów IP, z których będą akceptowane żądania. |
| Protokół |`spr=https` |Dozwolone są tylko żądania przy użyciu protokołu HTTPS. |
| Podpis |`sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D` |Używany do uwierzytelniania dostępu do obiektu blob. Podpis jest HMAC obliczona ciąg do logowania i klucz przy użyciu algorytm SHA256, a następnie kodowany w formacie Base64. |

### <a name="account-sas-uri-example"></a>Przykład identyfikatora URI połączenia SAS konta

Oto przykład konta sygnatury dostępu Współdzielonego, który używa tej samej typowych parametrów w tokenie. Ponieważ te parametry są opisane powyżej, ich nie zostały opisane w tym miejscu. Tylko parametry, które są specyficzne dla konta, które SAS są opisane w poniższej tabeli.

```
https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B
```

| Name (Nazwa) | Część SAS | Opis |
| --- | --- | --- |
| Identyfikator URI zasobu |`https://myaccount.blob.core.windows.net/?restype=service&comp=properties` |Punkt końcowy usługi Blob, wraz z parametrami pobierania właściwości usługi (jeśli jest wywoływana z GET) lub właściwości usługi (jeśli jest wywoływana z ZESTAWEM). |
| Usługi |`ss=bf` |Sygnatury dostępu Współdzielonego ma zastosowanie do usług obiektów Blob i plików |
| Typy zasobów |`srt=s` |Sygnatury dostępu Współdzielonego ma zastosowanie do operacji na poziomie usługi. |
| Uprawnienia |`sp=rw` |Uprawnienia udzielić dostępu do odczytu i zapisu. |

Biorąc pod uwagę, że uprawnienia są ograniczone do poziomu usługi, są dostępne operacje przy użyciu tego SAS **pobrać właściwości usługi Blob** (odczyt) i **ustawić właściwości usługi Blob** (zapis). Jednak z zasobem inny identyfikator URI tego samego tokenu sygnatury dostępu Współdzielonego można również delegować dostęp do **uzyskać statystyki usługi Blob** (odczyt).

## <a name="controlling-a-sas-with-a-stored-access-policy"></a>Kontrolowanie SAS z zasadami dostępu przechowywane
Sygnatury dostępu współdzielonego, można wykonać jedną z dwóch formach:

* **Ad hoc sygnatury dostępu Współdzielonego:** podczas tworzenia ad hoc SAS, godzina rozpoczęcia, czas wygaśnięcia i uprawnienia dla sygnatury dostępu Współdzielonego są wszystkie określony w identyfikatorze URI SAS (lub domniemanych, w przypadku, gdy zostanie pominięty czas rozpoczęcia). Ten typ sygnatury dostępu Współdzielonego można utworzyć jako konto SAS lub sygnatury dostępu Współdzielonego usługi.
* **Sygnatury dostępu Współdzielonego z zasad dostępu przechowywanej:** zasady dostępu do przechowywanych jest zdefiniowany w kontenerze zasobów--kontenera obiektów blob, tabeli, kolejki, lub udziału--plików i może służyć do zarządzania ograniczeniami dla jednego lub więcej sygnatur dostępu współdzielonego. Po skojarzeniu sygnatury dostępu Współdzielonego za pomocą zasad dostępu przechowywane, sygnatury dostępu Współdzielonego dziedziczy ograniczenia — czas rozpoczęcia, czas wygaśnięcia i uprawnienia — zdefiniowane zasady dostępu przechowywane.

> [!NOTE]
> Obecnie sygnatura dostępu Współdzielonego konta musi być SAS ad hoc. Przechowywane dostępu, zasady nie są jeszcze obsługiwane dla konta sygnatury dostępu Współdzielonego.

Różnica między dwoma formularzami jest ważne dla klucza scenariusz: odwołania. Ponieważ identyfikator URI sygnatury dostępu Współdzielonego jest adres URL, każdy użytkownik, który uzyskuje sygnatury dostępu Współdzielonego może być używany, niezależnie od tego, który został pierwotnie utworzony. Sygnatury dostępu Współdzielonego zostanie opublikowana publicznie, może służyć wszystkim osobom na świecie. Sygnatury dostępu Współdzielonego udziela dostępu do zasobów innym osobom posiadającym go, dopóki jedna z następujących czterech zdarzeń sytuacji:

1. Czas wygaśnięcia określony na sygnatury dostępu Współdzielonego zostanie osiągnięty.
2. Czas wygaśnięcia określone w zasadach dostępu składowana odwołuje się sygnatury dostępu Współdzielonego osiągnięciu (jeśli istnieje odwołanie do zasad dostępu do przechowywanych i określa czas wygaśnięcia). Może to występować, ponieważ interwał upływa lub zmodyfikowano zasad dostępu przechowywane z upływem czasu w przeszłości, który jest jednym ze sposobów odwołać dostępu Współdzielonego.
3. Zasady dostępu składowana odwołuje się sygnatury dostępu Współdzielonego jest usunięte, sposób, aby można było odwołać dostępu Współdzielonego. Należy pamiętać, że możesz ponownie utworzyć zasady dostępu do przechowywanych o dokładnie takiej samej nazwie, wszystkie istniejące tokeny sygnatury dostępu Współdzielonego ponownie będzie nieprawidłowa według uprawnienia skojarzone z tym zasadom dostępu przechowywanych (przy założeniu, że czas wygaśnięcia na sygnatury dostępu Współdzielonego nie przeszło). Mają zamiar odwołać dostępu Współdzielonego, należy użyć innej nazwy, jeśli należy ponownie utworzyć zasady dostępu z upływem czasu w przyszłości.
4. Ponownego wygenerowania klucza konta, który został użyty do utworzenia sygnatury dostępu Współdzielonego. Trwa ponowne generowanie klucza konta spowoduje, że wszystkie składniki aplikacji przy użyciu tego klucza do uwierzytelnione, dopóki nie jest zaktualizowane do używania konta nowo wygenerowano ponownie klucz lub klucz prawidłowego konta.

> [!IMPORTANT]
> Identyfikator URI sygnatury dostępu współdzielonego jest skojarzony z kluczem konta używane do tworzenia podpisu i skojarzonych przechowywane zasad dostępu (jeśli istnieje). Jeśli określono żadnych zasad dostępu przechowywane, jedynym sposobem, aby odwołać sygnatury dostępu współdzielonego jest zmienić klucz konta.

## <a name="authenticating-from-a-client-application-with-a-sas"></a>Uwierzytelniania z aplikacji klienta z sygnatury dostępu Współdzielonego
Klient, który znajduje się w posiadaniu sygnatury dostępu Współdzielonego służy sygnatury dostępu Współdzielonego do uwierzytelniania żądań do konta magazynu, dla którego nie posiadają klucze konta. Sygnatury dostępu Współdzielonego może być uwzględniona w parametrach połączenia lub używana bezpośrednio z odpowiedniego konstruktora lub metody.

### <a name="using-a-sas-in-a-connection-string"></a>W parametrach połączenia przy użyciu sygnatury dostępu Współdzielonego
[!INCLUDE [storage-use-sas-in-connection-string-include](../../../includes/storage-use-sas-in-connection-string-include.md)]

### <a name="using-a-sas-in-a-constructor-or-method"></a>Przy użyciu sygnatury dostępu Współdzielonego w konstruktorze lub — metoda
Kilka konstruktorów biblioteki klienta magazynu Azure i przeciążenia metody oferują parametrem sygnatury dostępu Współdzielonego, aby mógł uwierzytelnić żądania do usługi z sygnatury dostępu Współdzielonego.

Na przykład tutaj identyfikator URI sygnatury dostępu Współdzielonego służy do tworzenia odwołanie do blokowego obiektu blob. Sygnatury dostępu Współdzielonego zapewnia poświadczenia tylko wymagane dla żądania. Odwołanie do blokowych obiektów blob są używane do operacji zapisu:

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
Korzystając z sygnatury dostępu współdzielonego w aplikacjach, należy znać dwa potencjalne zagrożenia:

* Przeciek sygnatury dostępu Współdzielonego może służyć każdego, kto uzyskuje, które potencjalnie może naruszyć bezpieczeństwo Twojego konta magazynu.
* Jeśli podano sygnatury dostępu Współdzielonego wygasa aplikacja kliencka aplikacji nie może pobrać nowe SAS z usługi, a następnie mogą napotykać funkcjonalności aplikacji.

Poniższe zalecenia dotyczące używania sygnatur dostępu współdzielonego mogą pomóc ograniczyć te zagrożenia:

1. **Zawsze używać protokołu HTTPS** do tworzenia i rozpowszechniania sygnatury dostępu Współdzielonego. Jeśli sygnatury dostępu Współdzielonego jest przekazywane za pośrednictwem protokołu HTTP i przechwycone, osoba przeprowadzająca atak typu man-in--middle jest możliwy odczyt sygnatury dostępu Współdzielonego i użyj go tak samo, jak wybranego użytkownika może mieć potencjalnie naruszania danych poufnych lub zezwala na uszkodzenie danych przez złośliwego użytkownika.
2. **Odwołanie przechowywane zasady dostępu, jeśli jest to możliwe.** Zasady dostępu do przechowywanych zapewniają opcję, aby odwołać uprawnienia bez konieczności ponownie wygenerować kluczy konta magazynu. Ustawienia okresu ważności na te bardzo daleko w przyszłości (lub nieskończone) i upewnij się, że są regularnie aktualizowane, aby przenieść go dalej w przyszłości.
3. **Użyj najbliższej przyszłości czas wygaśnięcia na ad hoc SAS.** W ten sposób nawet w przypadku złamania zabezpieczeń sygnatury dostępu Współdzielonego, jest prawidłowa tylko dla przez krótki czas. Takie rozwiązanie jest szczególnie ważne, jeśli nie może odwoływać się zasady dostępu do przechowywanych. Czas wygaśnięcia najbliższej przyszłości również ograniczyć ilość danych, które mogą być zapisywane do obiektu blob ograniczając czas dostępne do przekazania do niej.
4. **Mieć automatycznie odnawiaj SAS w razie potrzeby klientów.** Klientów należy odnawiać SAS przed wygaśnięciem, aby umożliwić czasu dla prób, jeśli usługa oferująca sygnatury dostępu Współdzielonego jest niedostępny. Jeśli Twoje SAS jest przeznaczona do użycia dla niewielkiej liczby operacji natychmiastowe i krótkim okresie, które powinny zostać wykonane w terminie wygaśnięcia, następnie może to być konieczne, gdyż sygnatury dostępu Współdzielonego nie powinno być odnawiane. Jednak jeśli klient, który jest regularnie wysyłający żądania za pomocą sygnatury dostępu Współdzielonego, następnie możliwości wygaśnięcia wejścia play. Kwestia klucza jest równoważyć potrzeby SAS być krótkotrwały (jak wcześniej wspomniano) z koniecznością zapewnienia, że klient żąda odnowienia wczesne wystarczającej ilości (w celu uniknięcia przerw w działaniu z powodu SAS wygasa przed pomyślnym odnawiania).
5. **Należy zachować ostrożność, czas rozpoczęcia sygnatury dostępu Współdzielonego.** Jeśli na sygnatury dostępu Współdzielonego, aby ustawić czas rozpoczęcia **teraz**, następnie z powodu zegara pochylanie (różnice w bieżącej godziny zgodnie z różnych maszyn), błędów może występować sporadycznie w pierwszym kilka minut. Ogólnie rzecz biorąc ustawić czas rozpoczęcia w przeszłości co najmniej 15 minut. Lub nie należy ustawiać go, który ułatwi prawidłowe natychmiast we wszystkich przypadkach. Ogólnie rzecz biorąc to samo dotyczy również — czas wygaśnięcia należy pamiętać, że można zaobserwować maksymalnie 15 minut zegara pochylenia w żadnym kierunku na każde żądanie. W przypadku klientów z wersją REST przed 2012-02-12 maksymalny czas trwania SAS, która nie odwołuje się do zasad dostępu do przechowywanych jest 1 godziny, a wszystkie zasady Określanie długoterminowych niż który zakończy się niepowodzeniem.
6. **Być określony z zasobem, aby można było uzyskać dostęp.** Najbezpieczniejszym rozwiązaniem jest zapewnienie użytkownika przy minimalnych wymaganych uprawnień. Jeśli użytkownik potrzebuje tylko do odczytu w pojedynczej jednostki, następnie przyznać im dostęp do odczytu do tego pojedynczej jednostki, a nie odczytu/zapisu/usuwania dostęp do wszystkich jednostek. Ułatwia to również zmniejszyć szkody, jeśli sygnatury dostępu Współdzielonego zostanie naruszony, ponieważ sygnatury dostępu Współdzielonego ma mniej energii w ręce osoba atakująca.
7. **Dowiedz się, że Twoje konto będą naliczane za bez użycia, w tym odbywa się za pomocą sygnatury dostępu Współdzielonego.** Jeśli należy zapewnić dostęp do zapisu do obiektu blob, użytkownik może wybrać do przekazania obiektu blob 200GB. Jeśli zostały podane je również dostęp do odczytu, mogą one jej pobranie 10 razy ponoszenia 2 TB w kosztach wyjście dla Ciebie. Podaj ponownie, ograniczone uprawnienia, aby ułatwić uniknięcie potencjalnych akcji złośliwych użytkowników. Użyj sygnatury dostępu Współdzielonego w krótkim okresie, aby zmniejszyć to zagrożenie (ale być mając na uwadze zegara na czas zakończenia).
8. **Sprawdzanie poprawności danych napisane przy użyciu sygnatury dostępu Współdzielonego.** Gdy aplikacja kliencka zapisuje dane do konta magazynu, należy pamiętać, że mogą być problemy z tymi danymi. Jeśli aplikacja wymaga czy danych można sprawdzić poprawności lub autoryzacji, zanim będzie gotowy do użycia, należy wykonać tej weryfikacji po zapisaniu danych i zanim zostanie on użyty przez aplikację. Takie rozwiązanie chroni także uszkodzony lub złośliwymi danych zapisywane do swojego konta przez użytkownika, który prawidłowo nabyte sygnatury dostępu Współdzielonego lub przez użytkownika, wykorzystaniu ujawnione sygnatury dostępu Współdzielonego.
9. **Nie używaj sygnatury dostępu Współdzielonego.** Czasami ryzyko związane z określoną operację względem konta magazynu przeważają korzyści wynikające z sygnatury dostępu Współdzielonego. W takich operacjach utworzyć usługę warstwy środkowej, która zapisuje na koncie magazynu po wykonaniu firm reguły sprawdzania poprawności, uwierzytelnianie i inspekcji. Ponadto czasami jest łatwiejsze do zarządzania dostępem w inny sposób. Na przykład, jeśli chcesz wszystkie obiekty BLOB w kontenerze publicznie był bardziej czytelny, możesz wprowadzić kontenera publicznego, zamiast zapewnienie dostępu do wszystkich klientów sygnatury dostępu Współdzielonego.
10. **Umożliwia monitorowanie aplikacji analityka magazynu.** Rejestrowanie i metryk umożliwia obserwować żadnych kolekcji w niepowodzeń uwierzytelniania z powodu awarii w usłudze dostawcy SAS lub przypadkowego usuwania zasad dostępu przechowywane. Zobacz [Blog zespołu usługi Magazyn Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) Aby uzyskać dodatkowe informacje.

## <a name="sas-examples"></a>Przykłady SAS
Poniżej przedstawiono kilka przykładów oba rodzaje sygnatur dostępu współdzielonego, sygnatura dostępu Współdzielonego konta i usługi.

Aby uruchomić te przykłady C#, musisz odwołania się do następujących pakietów NuGet w projekcie:

* [Biblioteka klienta usługi Azure Storage dla platformy .NET](http://www.nuget.org/packages/WindowsAzure.Storage), wersja 6.x lub nowszego (pod kątem używania konta SAS).
* [Azure Configuration Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager)

Aby uzyskać dodatkowe przykłady pokazują, jak utworzyć i przetestować sygnatury dostępu Współdzielonego, zobacz [przykłady kodu platformy Azure dla magazynu](https://azure.microsoft.com/documentation/samples/?service=storage).

### <a name="example-create-and-use-an-account-sas"></a>Przykład: Tworzenie i używanie sygnatury dostępu Współdzielonego konta
Poniższy przykład kodu tworzy konto sygnatury dostępu Współdzielonego jest prawidłowa dla usług obiektów Blob i plików, która daje klientowi uprawnienia do odczytu, zapisu i listy uprawnień dostępu do interfejsów API z poziomu usługi. Sygnatura dostępu Współdzielonego konta ogranicza protokołu HTTPS, więc żądanie musi zostać wykonane z protokołu HTTPS.

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

Aby użyć sygnatura dostępu Współdzielonego konta dostępu do interfejsów API poziom usługi dla usługi obiektów Blob, utworzenia obiektu Blob klienta przy użyciu sygnatury dostępu Współdzielonego i punktu końcowego magazynu obiektów Blob dla konta magazynu.

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

### <a name="example-create-a-stored-access-policy"></a>Przykład: Tworzenie zasad dostępu przechowywane
Poniższy kod tworzy zasady przechowywane dostępu do kontenera. Zasady dostępu służy do określania warunków ograniczających dla sygnatury dostępu Współdzielonego usługi na kontener lub jego obiektów blob.

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

### <a name="example-create-a-service-sas-on-a-container"></a>Przykład: Tworzenie usługi SAS w kontenerze
Poniższy kod tworzy sygnatury dostępu Współdzielonego w kontenerze. Jeśli podano nazwę istniejących zasad dostępu przechowywane, że zasady są skojarzone z sygnatury dostępu Współdzielonego. Jeśli nie zasady przechowywane dostępu, zostanie następnie kod tworzy SAS ad hoc w kontenerze.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
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
        // It is also possible to specify some constraints on an ad-hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for blob container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

### <a name="example-create-a-service-sas-on-a-blob"></a>Przykład: Tworzenie sygnatury dostępu Współdzielonego usługi na obiektu blob
Poniższy kod tworzy sygnatury dostępu Współdzielonego na obiektu blob. Jeśli podano nazwę istniejących zasad dostępu przechowywane, że zasady są skojarzone z sygnatury dostępu Współdzielonego. Jeśli nie zasady przechowywane dostępu, zostanie następnie kod tworzy SAS ad hoc w obiekcie blob.

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
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters of an ad-hoc SAS, and
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
Sygnatury dostępu współdzielonego są przydatne do prezentowania ograniczone uprawnienia do konta magazynu do klientów, które nie powinny mieć klucz konta. Są one tak, to ważna część model zabezpieczeń dla dowolnej aplikacji przy użyciu usługi Azure Storage. Jeśli wykonujesz najlepszych rozwiązań, które są wymienione w tym miejscu można SAS zapewniają większą elastyczność dostępu do zasobów na koncie magazynu bez naruszania zabezpieczeń aplikacji.

## <a name="next-steps"></a>Następne kroki
* [Udostępnione sygnatur dostępu, część 2: Tworzenie i sygnatury dostępu Współdzielonego za pomocą magazynu obiektów Blob](../blobs/storage-dotnet-shared-access-signature-part-2.md)
* [Zarządzanie anonimowy dostęp do odczytu do kontenerów i obiektów blob](../blobs/storage-manage-access-to-resources.md)
* [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego](http://msdn.microsoft.com/library/azure/ee395415.aspx)
* [Wprowadzenie do tabeli i kolejki SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
