---
title: Dysk testowy usługi Azure Resource Manager | Azure Marketplace
description: Tworzenie dysku testowego w portalu Marketplace przy użyciu usługi Azure Resource Manager
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 6125aa010d8676518b84f866343b01f95246160f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275938"
---
# <a name="azure-resource-manager-test-drive"></a>Wersja testowa usługi Azure Resource Manager

Ten artykuł jest przeznaczony dla wydawców, którzy mają swoją ofertę w portalu Azure Marketplace lub którzy korzystają z usługi AppSource, ale chcą tworzyć ich testową jazdę tylko z zasobami platformy Azure.

Szablon usługi Azure Resource Manager (Resource Manager) to zakodowany kontener zasobów platformy Azure, który projektujesz, aby jak najlepiej reprezentować twoje rozwiązanie. Jeśli nie znasz szablonu Menedżera zasobów, przeczytaj [opis szablonów Menedżera zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) i [szablonów Menedżera zasobów,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) aby upewnić się, że wiesz, jak tworzyć i testować własne szablony.

Testowa dysku jest to, że ma dostarczony szablon Menedżera zasobów i sprawia, że wdrożenie wszystkich zasobów wymaganych z tego szablonu Menedżera zasobów do grupy zasobów.

Jeśli zdecydujesz się na tworzenie dysku testowego usługi Azure Resource Manager, wymagania są dla Ciebie:

- Skompiluj, przetestuj, a następnie przekaż szablon Menedżera zasobów dysku testowego.
- Skonfiguruj wszystkie wymagane metadane i ustawienia, aby włączyć jazdę testną.
- Ponownie opublikuj swoją ofertę z włączoną jazdą próbną.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Jak utworzyć dysk testowy usługi Azure Resource Manager

Oto proces tworzenia dysku testowego usługi Azure Resource Manager:

1. Zaprojektuj, co mają zrobić klienci na diagramie przepływu.
1. Zdefiniuj, jakie środowiska chcesz zbudować twoi klienci.
1. Na podstawie powyższych definicji zdecyduj, jakie elementy i zasoby są potrzebne klientom do osiągnięcia takiego doświadczenia: na przykład wystąpienie D365 lub witryna sieci Web z bazą danych.
1. Skompiluj projekt lokalnie i przetestuj środowisko.
1. Pakiet środowiska we wdrożeniu szablonu ARM, a stamtąd:
    1. Zdefiniuj, jakie części zasobów są parametrami wejściowymi;
    1. Jakie są zmienne;
    1. Jakie produkty są podane do doświadczenia klienta.
1. Publikowanie, testowanie i publikowanie na żywo.

Najważniejszą częścią tworzenia dysku testowego usługi Azure Resource Manager jest zdefiniowanie, jaki scenariuszy mają wystąpić klienci. Czy jesteś produktem zapory i chcesz demonstrować, jak dobrze radzisz sobie z atakami iniekcji skryptu? Czy jesteś produktem pamięci masowej i chcesz demonstrować, jak szybko i łatwo twoje rozwiązanie kompresuje pliki?

Pamiętaj, aby poświęcić wystarczająco dużo czasu na ocenę, jakie są najlepsze sposoby, aby pokazać swój produkt. W szczególności wokół wszystkich wymaganych zasobów, które byłyby potrzebne, ponieważ sprawia, że pakowanie szablonu Menedżera zasobów jest wystarczająco łatwiejsze.

Aby kontynuować nasz przykład zapory, architektura może być, że potrzebujesz publicznego adresu URL IP dla usługi i innego publicznego adresu URL IP dla witryny sieci Web, że zapora jest chronina. Każdy adres IP jest wdrażany na maszynie wirtualnej i połączony z sieciową grupą zabezpieczeń + interfejsem sieciowym.

Po zaprojektcie żądanego pakietu zasobów, teraz przychodzi zapisu i tworzenia szablonu Menedżera zasobów dysku testowego.

## <a name="writing-test-drive-resource-manager-templates"></a>Pisanie szablonów Menedżera zasobów dysku testowego

Test Drive uruchamia wdrożenia w trybie w pełni zautomatyzowanym i z tego powodu szablony dysku testowego mają pewne ograniczenia opisane poniżej.

### <a name="parameters"></a>Parametry

Większość szablonów ma zestaw parametrów. Parametry definiują nazwy zasobów, rozmiary zasobów (na przykład typy kont magazynu lub rozmiary maszyn wirtualnych), nazwy użytkowników i hasła, nazwy DNS i tak dalej. Podczas wdrażania rozwiązań przy użyciu witryny Azure portal, można ręcznie wypełnić wszystkie te parametry, wybrać dostępne nazwy DNS lub nazwy kont magazynu i tak dalej.

![Lista parametrów w usłudze Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Jednak test drive działa w trybie w pełni automatycznym, bez interakcji z człowiekiem, więc obsługuje tylko ograniczony zestaw kategorii parametrów. Jeśli parametr w szablonie Menedżera zasobów dysku testowego nie należy do jednej z obsługiwanych kategorii, należy **zastąpić ten parametr wartością zmienną lub stałą.**

Można użyć dowolnej prawidłowej nazwy parametrów, Test Drive rozpoznaje kategorię parametrów przy użyciu wartości typu metadanych. **Należy określić typ metadanych dla każdego parametru szablonu,** w przeciwnym razie szablon nie przejdzie sprawdzania poprawności:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

Ważne jest również, aby pamiętać, że **wszystkie parametry są opcjonalne,** więc jeśli\'nie chcesz używać żadnych, nie\'musisz.

### <a name="accepted-parameter-metadata-types"></a>Akceptowane typy metadanych parametrów

| Typ metadanych   | Typ parametru  | Opis     | Wartość próbki    |
|---|---|---|---|
| **Baseuri**     | ciąg          | Podstawowy identyfikator URI pakietu wdrożeniowego| https:\//\<\.. \>.blob.core.windows.net/\<\..\> |
| **Nazwę użytkownika**    | ciąg          | Nowa losowa nazwa użytkownika.| administrator68876      |
| **hasło**    | bezpieczny ciąg znaków    | Nowe hasło losowe | Lp!ACS\^2kh     |
| **identyfikator sesji**   | ciąg          | Unikatowy identyfikator sesji dysku testowego (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>Baseuri

Test Drive inicjuje ten parametr za pomocą **identyfikatora Uri pakietu** wdrażania, dzięki czemu można użyć tego parametru do konstruowania identyfikatora Uri dowolnego pliku dołączonego do pakietu.

```json
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Wewnątrz szablonu można użyć tego parametru do skonstruowania identyfikatora Uri dowolnego pliku z pakietu wdrażania dysku testowego. W poniższym przykładzie pokazano, jak skonstruować identyfikator Uri połączonego szablonu:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>nazwa użytkownika

Test Drive inicjuje ten parametr przy nowej losowej nazwie użytkownika:

```json
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Wartość próbki:

    admin68876

Możesz użyć losowych lub stałych nazw użytkowników dla rozwiązania.

#### <a name="password"></a>hasło

Test Drive inicjuje ten parametr przy pomocą nowego hasła losowego:

```json
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Wartość próbki:

    Lp!ACS^2kh

Do rozwiązania można użyć haseł losowych lub stałych.

#### <a name="session-id"></a>Identyfikator sesji

Dysk testowy inicjuje ten parametr za pomocą unikatowego identyfikatora GUID reprezentującego identyfikator sesji dysku testowego:

```json
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique Test Drive session id."
    }
  },
  ...
}
```

Wartość próbki:

    b8c8693e-5673-449c-badd-257a405a6dee

Ten parametr służy do jednoznacznej identyfikacji sesji dysku testowego, jeśli jest to konieczne.

### <a name="unique-names"></a>Unikatowe nazwy

Niektóre zasoby platformy Azure, takie jak konta magazynu lub nazwy DNS, wymagają unikatowych nazw globalnie.

Oznacza to, że za każdym razem, gdy testowa jazda wdraża szablon Menedżera\' zasobów, tworzy **nową grupę zasobów o unikatowej nazwie** dla wszystkich swoich zasobów. W związku z tym jest wymagane, aby użyć [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) funkcji łączone z nazwami zmiennych na identyfikatory grupy zasobów do generowania losowych unikatowych wartości:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Upewnij się, że łączysz parametry/zmienne ciągi (contosovm)\'z unikatowym wyjściem\'ciągu (resourceGroup().id),\'\'ponieważ gwarantuje to unikatowość i niezawodność każdej zmiennej.

Na przykład większość nazw zasobów nie może zaczynać się od cyfry, ale funkcja unikatowego ciągu może zwracać ciąg, który rozpoczyna się od cyfry. Tak jeśli używasz nieprzetworzonych unikatowy ciąg danych wyjściowych, wdrożenia zakończy się niepowodzeniem. 

Dodatkowe informacje na temat reguł i ograniczeń nazewnictwa zasobów można znaleźć w [tym artykule](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Lokalizacja wdrożenia

Możesz udostępnić jazdę testową w różnych regionach platformy Azure. Chodzi o to, aby umożliwić użytkownikowi wybrać najbliższy region, aby zapewnić środowisko użytkownika bestii.

Gdy testowa firma tworzy wystąpienie laboratorium, zawsze tworzy grupę zasobów w regionie wybranym przez użytkownika, a następnie wykonuje szablon wdrożenia w tym kontekście grupy. Dlatego szablon powinien wybrać lokalizację wdrożenia z grupy zasobów:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A następnie użyj tej lokalizacji dla każdego zasobu dla określonego wystąpienia laboratorium:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Należy upewnić się, że subskrypcja może wdrożyć wszystkie zasoby, które chcesz wdrożyć w każdym z regionów, które wybierasz. Ponadto należy upewnić się, że obrazy maszyny wirtualnej są dostępne we wszystkich regionach, które mają być włącznie, w przeciwnym razie szablon wdrożenia nie będzie działać w niektórych regionach.

### <a name="outputs"></a>Dane wyjściowe

Zwykle za pomocą szablonów Menedżera zasobów można wdrożyć bez tworzenia żadnych danych wyjściowych. Dzieje się tak, ponieważ znasz wszystkie wartości używane do wypełniania parametrów szablonu i zawsze można ręcznie sprawdzić właściwości dowolnego zasobu.

W przypadku szablonów Menedżera zasobów\'dysku testowego ważne jest jednak zwrócenie do dysku testowego wszystkich informacji, które są wymagane do uzyskania dostępu do laboratorium (identyfikatory URI witryny sieci Web, nazwy hostów maszyn wirtualnych, nazwy użytkowników i hasła). Upewnij się, że wszystkie nazwy danych wyjściowych są czytelne, ponieważ te zmienne są prezentowane klientowi.

Nie ma żadnych ograniczeń związanych z wyjściami szablonu. Pamiętaj tylko, że test konwertuje wszystkie wartości wyjściowe na **ciągi,** więc jeśli wyślesz obiekt do danych wyjściowych, użytkownik zobaczy ciąg JSON.

Przykład:

```json
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Limity subskrypcji

Jeszcze jedna rzecz, którą należy wziąć pod uwagę, to limity subskrypcji i usług. Na przykład jeśli chcesz wdrożyć maksymalnie dziesięć 4-rdzeniowych maszyn wirtualnych, musisz upewnić się, że subskrypcja używana w laboratorium umożliwia użycie 40 rdzeni.

Więcej informacji na temat limitów subskrypcji i usług platformy Azure można znaleźć w [tym artykule](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Ponieważ można podjąć wiele dysków testowych w tym samym \# czasie, sprawdź, czy subskrypcja może obsłużyć rdzenie pomnożone przez całkowitą liczbę równoczesnych dysków testowych, które mogą być podjęte.

### <a name="what-to-upload"></a>Co przesłać

Szablon Menedżera zasobów dysku testowego jest przekazyany jako plik zip, który może zawierać różne artefakty wdrażania, ale musi mieć jeden plik o nazwie **main-template.json**. Ten plik jest szablonem wdrażania usługi Azure Resource Manager, a testowa dysk używa go do tworzenia wystąpienia laboratorium.

Jeśli masz dodatkowe zasoby poza tym plikiem, można odwoływać się do niego jako zasób zewnętrzny wewnątrz szablonu lub można dołączyć zasób w pliku zip.

Podczas certyfikacji publikowania testowa dysk rozpina pakiet wdrażania i umieszcza jego zawartość w wewnętrznym kontenerze obiektów blob na dysku testowym. Struktura kontenera odzwierciedla strukturę pakietu wdrażania:

| package.zip                       | Kontener obiektów blob dysku testowego         |
|---|---|
| główny szablon.json                | https:\//\<\... \>.blob.core.windows.net/\<\... \>/main-template.json  |
| szablony/solution.json           | https:\//\<\... \>.blob.core.windows.net/\<\... \>/templates/solution.json |
| skrypty/rozgrzewka.ps1                | https:\//\<\... \>.blob.core.windows.net/\<\... \>/scripts/warmup.ps1  |


Nazywamy uri tego kontenera obiektów blob Base Uri. Każda wersja laboratorium ma swój własny kontener obiektów blob, a zatem każda wersja laboratorium ma swój własny podstawowy identyfikator Uri. Test drive można przekazać uri podstawowy rozpakowanego pakietu wdrażania do szablonu za pomocą parametrów szablonu.

## <a name="transforming-template-examples-for-test-drive"></a>Przekształcanie przykładów szablonów dla dysku testowego

Proces przekształcania architektury zasobów w szablon Menedżera zasobów dysku testowego może być trudny. Aby ułatwić ten proces, zrobiliśmy\'przykłady, jak najlepiej [przekształcić obecne szablony wdrażania tutaj](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Jak opublikować jazdę próbną

Teraz, gdy masz wbudowany testowy, w tej sekcji przechodzi przez każde z pól wymaganych do pomyślnego opublikowania dysku testowego.

![Włączanie dysku testowego w interfejsie użytkownika](./media/azure-resource-manager-test-drive/howtopub1.png)

Pierwszym i najważniejszym polem jest przełączanie, czy chcesz włączyć jazdę testową dla oferty, czy nie. Po **wybraniu opcji Tak,** pozostała część formularza ze wszystkimi wymaganymi polami zostanie wyświetlona do wypełnienia. Po wybraniu opcji Nie formularz zostanie **wyłączony,** a ponowne opublikowanie dysku testowego zostanie wyłączone, dysk testowy zostanie usunięty z produkcji.

Uwaga: Jeśli istnieją dyski testowe aktywnie używane przez użytkowników, te dyski testowe będą nadal działać do czasu wygaśnięcia ich sesji.

### <a name="details"></a>Szczegóły

Następna sekcja do wypełnienia to szczegóły dotyczące oferty testowej.

![Test Drive szczegółowe informacje](./media/azure-resource-manager-test-drive/howtopub2.png)

**Opis —** *wymagane* Jest to, gdzie piszesz główny opis tego, co znajduje się na dysku testowym. Klient przyjdzie tutaj, aby przeczytać, jakie scenariusze test drive będzie pokrycie o produkcie. 

**Instrukcja obsługi —** *wymagane* Jest to szczegółowy przewodnik po doświadczeniu dysku testowego. Klient otworzy to i może przejść przez dokładnie to, co chcesz, aby zrobić w całej ich jazdy testowej. Ważne jest, aby ta treść była łatwa do zrozumienia i naśladowania! (Musi to być plik .pdf)

**Test Drive Demo Video -** *Zalecane* Podobne do instrukcji obsługi, najlepiej jest dołączyć film instruktażowy doświadczenia z jazdy testowej. Klient będzie oglądać to przed lub podczas jazdy testowej i może przejść przez dokładnie to, co chcesz, aby zrobić w całej ich jazdy testowej. Ważne jest, aby ta treść była łatwa do zrozumienia i naśladowania!

- **Nazwa** — tytuł filmu
- **Link** — musi być osadzonym adresem URL z twojej rury lub filmu. Przykład, jak uzyskać osadzony adres URL, znajduje się poniżej:
- **Miniatura** — musi być obrazem wysokiej jakości (533x324) pikseli. Zaleca się wykonanie zrzutu ekranu przedstawiającego część środowiska z jazdy próbnej tutaj.

Poniżej opisano, jak te pola są wyświetlane dla klienta podczas korzystania z jazdy testowej.

![Lokalizacja pól Dysku testowego w ofercie Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Konfiguracja techniczna

Następna sekcja do wypełnienia jest miejsce, w którym przekazać szablon Menedżera zasobów dysku testowego i określić, jak konkretnie działają wystąpienia dysku testowego.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Wystąpienia —** *wymagane* Jest to, gdzie można skonfigurować, ile wystąpień chcesz, w jakim regionie i jak szybko klienci mogą uzyskać jazdę testową.

- **Wystąpienia** — w wybranych regionach można wybrać miejsce wdrażania szablonu Menedżera zasobów dysku testowego. Zaleca się, aby wybrać tylko jeden region, w którym najbardziej oczekujesz, że twoi klienci będą się znajdować.
- **Hot** — liczba wystąpień dysku testowego, które są już wdrożone i oczekują na dostęp dla wybranego regionu. Klienci mogą natychmiast uzyskać dostęp do tych dysków testowych, zamiast czekać na wdrożenie. Kompromisem jest to, że te wystąpienia są zawsze uruchomione w ramach subskrypcji platformy Azure, więc poniosą one większy koszt czasu pracy bez przestojów. Zdecydowanie zaleca się mieć **co najmniej jedno wystąpienie Hot,** ponieważ większość klientów nie chce czekać na zakończenie pełnych wdrożeń, a więc jest drop-off w użyciu klienta.
- **Ogrzać** — liczba wystąpień dysku testowego na region, który został wdrożony, a następnie maszyna wirtualna została zatrzymana i przechowywana w magazynie platformy Azure. Czas oczekiwania na warm wystąpień jest wolniejszy niż Hot wystąpień, ale czas pracy jest również tańsze.
- **Zimno** — liczba wystąpień dysku testowego na region, który może być wdrożony. Zimne wystąpienia wymagają całego szablonu Menedżera zasobów dysku testowego, aby przejść przez wdrożenie w czasie, gdy klient żąda dysku testowego, więc jest wolniejszy niż wystąpienia gorące lub ciepłe. Jednak kompromisem jest to, że trzeba zapłacić tylko za czas trwania jazdy testowej.

W tej chwili oblicza całkowitą liczbę potencjalnych równoczesnych dysków testowych, które zamierzasz udostępnić, i sprawdź, czy limit przydziału dla subskrypcji może obsłużyć tę równoczesną kwotę:

**(Liczba regionów wybranych x Gorące wystąpienia) + (Liczba regionów wybranych x Ciepłe wystąpienia) + (Liczba regionów wybranych x Zimne wystąpienia)**

**Czas trwania dysku testowego (w godzinach) -** *Wymagany* czas \# trwania, przez jaki czas jazdy testowej pozostanie aktywny, w godzinach. Dysk testowy kończy się automatycznie po zakończeniu tego okresu.

**Szablon Menedżera zasobów dysku testowego —** *wymagane* przekazanie szablonu Menedżera zasobów tutaj. Jest to plik zbudowany w poprzedniej sekcji powyżej. Nazwij główny plik szablonu: "main-template.json" i upewnij się, że szablon Menedżera zasobów zawiera parametry wyjściowe dla potrzebnych zmiennych kluczowych. (Musi to być plik zip)

**Informacje o dostępie —** *wymagane* po tym, jak klient otrzyma testową jazdę, informacje o dostępie są im prezentowane. Te instrukcje są przeznaczone do udostępniania przydatnych parametrów wyjściowych z szablonu Menedżera zasobów dysku testowego. Aby uwzględnić parametry wyjściowe, należy użyć podwójnych nawiasów klamrowych (na przykład **{{outputname}}**) i zostaną one poprawnie wstawione w lokalizacji. (Formatowanie ciągów HTML jest zalecane w tym miejscu do renderowania w frontonie).

### <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testowego

Ostatnią sekcją do wypełnienia jest możliwość automatycznego wdrażania dysków testowych przez połączenie subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Szczegóły subskrypcji wdrożenia dysku testowego](./media/azure-resource-manager-test-drive/subdetails1.png)

**Identyfikator subskrypcji platformy Azure —** *wymagane* Zapewnia dostęp do usług platformy Azure i witryny Azure portal. Subskrypcja jest, gdzie użycie zasobów jest zgłaszane i usługi są rozliczane. Jeśli nie masz jeszcze **tylko oddzielnej** subskrypcji platformy Azure tylko dla dysków testowych, śmiało i ją wykonaj. Identyfikatory subskrypcji platformy Azure można znaleźć, logując się do witryny Azure portal i przechodząc do subskrypcji w menu po lewej stronie. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subskrypcje platformy Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**Identyfikator dzierżawy usługi Azure AD —** *wymagane* Jeśli masz identyfikator dzierżawy już dostępny, możesz go znaleźć poniżej w identyfikatorze właściwości —\> katalog.

![Właściwości usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails3.png)

W przeciwnym razie utwórz nową dzierżawę w usłudze Azure Active Directory.

![Lista dzierżaw usługi Azure Active Directory](./media/azure-resource-manager-test-drive/subdetails4.png)

![Definiowanie organizacji, domeny i kraju/regionu dla dzierżawy usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Potwierdź wybór](./media/azure-resource-manager-test-drive/subdetails6.png)

**Identyfikator aplikacji usługi Azure AD —** *wymagane* Następnym krokiem jest utworzenie i zarejestrowanie nowej aplikacji. Użyjemy tej aplikacji do wykonywania operacji w wystąpieniu dysku testowego.

1. Przejdź do nowo utworzonego katalogu lub już istniejącego katalogu i wybierz pozycję Usługa Azure Active directory w okienku filtrów.
2. Wyszukaj "Rejestracje aplikacji" i kliknij "Dodaj"
3. Podaj nazwę aplikacji.
4. Wybierz typ jako "Aplikacja internetowa / API"
5. Podaj dowolną wartość w adresie URL logowania, nie będziemy\'używać tego pola.
6. Kliknij pozycję Utwórz.
7. Po utworzeniu aplikacji przejdź do właściwości\> — ustaw aplikację jako wielodostępną i naciśnij zapisz.

Kliknij pozycję Zapisz. Ostatnim krokiem jest, aby pobrać identyfikator aplikacji dla tej zarejestrowanej aplikacji i wkleić go w polu Test Drive tutaj.

![Szczegóły identyfikatora aplikacji usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Biorąc pod uwagę, że używamy aplikacji do wdrożenia w ramach subskrypcji, musimy dodać aplikację jako współautora subskrypcji. Instrukcje dla tych są jak poniżej:

1. Przejdź do bloku Subskrypcje i wybierz odpowiednią subskrypcję, która jest używany tylko dla dysku testowego.
1. Kliknij pozycję **Kontrola dostępu (IAM)**.
1. Kliknij kartę **Przypisania ról.**  ![Dodawanie nowego podmiotu kontroli dostępu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kliknij **pozycję Dodaj przypisanie roli**.
1. Ustaw rolę **współautora**.
1. Wpisz nazwę aplikacji usługi Azure AD i wybierz aplikację, aby przypisać rolę.
    ![Dodawanie uprawnień](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kliknij przycisk **Zapisz**.

**Klucz aplikacji usługi Azure AD —** *wymagane* Ostatecznym polem jest wygenerowanie klucza uwierzytelniania. W obszarze klawisze dodaj opis klucza, ustaw czas trwania, aby nigdy nie wygaśnie, a następnie wybierz pozycję Zapisz. Ważne **jest,** aby uniknąć posiadania wygasłego klucza, który spowoduje przerwanie jazdy testowej w produkcji. Skopiuj tę wartość i wklej ją do wymaganego pola Dysku testowego.

![Pokazuje klucze dla aplikacji usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz wszystkie pola jazdy testowej wypełnione, przejdź i **ponownie opublikować** swoją ofertę. Po przejściu certyfikacji na dysku testowym należy przetestować doświadczenie klienta w **wersji zapoznawczej** oferty. Uruchom dysk testowy w interfejsie użytkownika, a następnie otwórz subskrypcję platformy Azure w witrynie Azure portal i sprawdź, czy dyski testowe są w pełni wdrażane poprawnie.

![Portal Azure](./media/azure-resource-manager-test-drive/subdetails9.png)

Należy pamiętać, że nie należy usuwać żadnych wystąpień dysku testowego, ponieważ są one aprowidzone dla klientów, więc usługa testowej automatycznie wyczyści te grupy zasobów po zakończeniu z nim przez klienta.

Gdy poczujesz się komfortowo z ofertą podglądu, teraz nadszedł czas, aby **przejść na żywo!** Istnieje proces ostatecznego przeglądu od firmy Microsoft po opublikowaniu oferty, aby dokładnie sprawdzić całe doświadczenie od końca do końca. Jeśli z jakiegoś powodu oferta zostanie odrzucona, wyślemy powiadomienie do kontaktu inżyniera dla Twojej oferty, wyjaśniając, co będzie trzeba naprawić.

Jeśli masz więcej pytań, szukasz porad dotyczących rozwiązywania problemów lub chcesz zwiększyć sukces na dysku testowym, przejdź do [faq, rozwiązywania problemów & najlepszych praktyk.](./marketing-and-best-practices.md)
