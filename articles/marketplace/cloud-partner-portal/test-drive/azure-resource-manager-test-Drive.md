---
title: Usługa Azure Resource Manager testowej | Dokumentacja firmy Microsoft
description: Tworzenie wersję testową w portalu Marketplace przy użyciu usługi Azure Resource Manager
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick .Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2fe1a1fc1ff82415a5c2f88c72ae707b12cd5283
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774525"
---
# <a name="azure-resource-manager-test-drive"></a>Wersja testowa usługi Azure Resource Manager

Ten artykuł jest dla wydawców, którzy mają swoją ofertę w portalu Azure Marketplace lub korzystający z usługi AppSource, ale chcesz tworzyć ich wersji testowej przy użyciu tylko zasobów platformy Azure.

Szablon usługi Azure Resource Manager (Resource Manager) jest kontenerem kodowane zasobów platformy Azure, projektować do reprezentują najlepsze rozwiązania. Jeśli nie jesteś zaznajomiony z szablonu usługi Resource Manager, zapoznaj się [zrozumienie szablonów usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) i [Tworzenie szablonów usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) aby upewnić się, gdy wiesz, jak do tworzenia i testowania własnych szablonów.

Co to jest wersja testowa jest przyjmuje podany szablon usługi Resource Manager i sprawia, że wdrożenie programu wszystkie zasoby, które są wymagane z tego szablonu usługi Resource Manager w grupie zasobów.

W przypadku tworzenia dysku z systemem Azure Resource Manager Test wymagań jest umożliwia:

- Tworzenia, testowania i następnie Przekaż szablon Menedżer zasobów testowych dysku.
- Skonfiguruj wszystkie wymagane metadane i ustawienia, aby umożliwić wersji testowej.
- Za pomocą wersji testowej włączone, należy ponownie opublikować ofertę.

## <a name="how-to-build-an-azure-resource-manager-test-drive"></a>Jak utworzyć testowej usługi Azure Resource Manager

Poniżej przedstawiono proces tworzenia wersji testowej usługi Resource Manager platformy Azure:

1. Projektuj, co chcesz, aby klienci w diagramu przepływu.
1. Zdefiniuj, jakiego środowiska, którą chcesz klientów do tworzenia.
1. Oparte na powyższych definicjach, zdecyduj, jakie elementy i zasoby, które są wymagane dla klientów osiągnąć takie środowisko: na przykład wystąpienie D365 lub witryny sieci Web z bazą danych.
1. Kompilowanie projektu lokalnie i testowanie doświadczenia.
1. Pakiet środowiska we wdrożeniu szablonu ARM i z tego miejsca:
    1. Określają, jakie części zasobów są parametrami wejściowymi;
    1. Co to są zmienne;
    1. Jakie dane wyjściowe są przydzielone na obsługę klientów.
1. Publikowanie, test i przejdź na żywo.

To najważniejszy element o tworzeniu usługi Azure Resource Manager testowej do zdefiniowania, jakie scenario(s) użytkownicy powinni się. Czy program zapory i chcesz pokaz, jak obsługiwać ataki przez iniekcję kodu skryptu? Czy można produktu magazynu i chcesz pokaz, jak szybko i łatwo, rozwiązania kompresuje pliki?

Upewnij się, które możesz wydać wystarczająca ilość czasu, ocenianie, jakie są najlepsze sposoby stażysta produktu. Specjalnie wokół wszystkich wymaganych zasobów może być konieczne, ponieważ ułatwia pakowanie wystarczająco łatwiejsze szablonu usługi Resource Manager.

Aby kontynuować z naszego przykładu zapory, architektury może być konieczne publiczny adres IP URL usługi i inny publiczny adres IP URL dla witryny sieci Web, która chroni zapora. Każdego adresu IP jest wdrożone na maszynie wirtualnej i połączone razem z grupy zabezpieczeń sieci i interfejsu sieciowego.

Po zaprojektowaniu żądanego pakietu zasobów zawiera teraz zapisywania i tworzenia szablonu Menedżera zasobów testowych z dysku.

## <a name="writing-test-drive-resource-manager-templates"></a>Pisanie testów dysków szablonów usługi Resource Manager

Wersja testowa uruchamia wdrożeń w trybie w pełni zautomatyzowane i z tego powodu, szablony wersji testowej mają pewne ograniczenia opisane poniżej.

### <a name="parameters"></a>Parametry

Większość szablony mają zestaw parametrów. Parametry definiują nazwy zasobów, rozmiarów zasobów (na przykład typy kont magazynu lub rozmiarów maszyn wirtualnych), nazwy użytkownika i hasła, nazw DNS i tak dalej. Podczas wdrażania rozwiązania przy użyciu witryny Azure portal, można ręcznie wypełnić te parametry, wybierz dostępne nazwy DNS lub nazwy kont magazynu i tak dalej.

![Lista parametrów w usłudze Azure Resource Manager](./media/azure-resource-manager-test-drive/param1.png)

Wersja testowa działa jednak w trybie pełni automatyczna, bez interakcji z człowiekiem, obsługuje tylko ograniczony zestaw kategorii parametru. Jeśli parametr w szablonie Menedżera zasobów testowych z dysku nie należą do jednego z obsługiwanych kategorii, musisz najpierw **zastąpienie tego parametru z wartością zmiennej czy stałej.**

Można użyć dowolnego prawidłową nazwę parametry, wersji testowej rozpoznaje kategoria parametru przy użyciu wartości typ metadanych. Możesz **należy określić typ metadanych dla każdego parametru szablonu**, w przeciwnym razie szablon nie zostanie pomyślnie weryfikacji:

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

Jest również pamiętać, że **wszystkie parametry są opcjonalne**, więc jeśli nie\'t chcesz używać żadnych, komputer\'było.

### <a name="accepted-parameter-metadata-types"></a>Parametr akceptowane typy metadanych

| Typ metadanych   | Typ parametru  | Opis     | Przykładowa wartość    |
|---|---|---|---|
| **BaseUri**     | ciąg          | Podstawowy identyfikator URI pakietu wdrażania| https:\//\<\..\>.blob.core.windows.net/\<\..\> |
| **Nazwa użytkownika**    | ciąg          | Nowa nazwa użytkownika losowych.| admin68876      |
| **Hasło**    | bezpieczny ciąg    | Losowe hasło. | LP! ACS\^2kh     |
| **Identyfikator sesji**   | ciąg          | Unikatowe sesji testowej identyfikator (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="username"></a>nazwa użytkownika

Wersja testowa inicjuje tego parametru z **podstawowy identyfikator Uri** z pakietu wdrożeniowego, więc ten parametr służy do konstruowania identyfikatorów Uri pliku dołączone do pakietu.

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

Wewnątrz szablonu ten parametr służy do utworzenia identyfikatora Uri dowolny plik z pakietu wdrażania wersji testowej. W poniższym przykładzie pokazano sposób tworzenia identyfikatora Uri połączonego szablonu:

```json
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>nazwa użytkownika

Wersja testowa inicjuje tego parametru z nową nazwą użytkownika losowego:

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

Wartość próbek:

    admin68876

Za pomocą losowych lub stała nazw użytkowników dla Twojego rozwiązania.

#### <a name="password"></a>hasło

Wersja testowa inicjuje parametru za pomocą nowego hasła losowego:

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

Wartość próbek:

    Lp!ACS^2kh

Dla danego rozwiązania, można użyć hasła losowego lub stałą.

#### <a name="session-id"></a>Identyfikator sesji

Wersja testowa zainicjować tego parametru z Unikatowy identyfikator GUID reprezentujący identyfikator sesji testowej:

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

Wartość próbek:

    b8c8693e-5673-449c-badd-257a405a6dee

Ten parametr służy do jednoznacznego identyfikowania sesji testowej w razie potrzeby.

### <a name="unique-names"></a>Unikatowe nazwy

Zasoby platformy Azure, takich jak konta magazynu lub nazwy DNS wymaga globalnie unikatowe nazwy.

Oznacza to, że za każdym razem, gdy wersja testowa służy do wdrażania szablonu usługi Resource Manager, tworzy ono **nową grupę zasobów o unikatowej nazwie** dla wszystkich jego\' zasobów. W związku z tym jest wymagany do użycia [uniquestring](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions#uniquestring) funkcja połączona z Twojej nazwy zmiennych w grupie zasobów identyfikatorów do wygenerowania losowych unikatowe wartości:

```json
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Upewnij się, ciągów z parametrem/zmiennej (\'contosovm\') z danymi wyjściowymi unikatowy ciąg (\'resourceGroup () .id\'), ponieważ jest to gwarancją unikatowości i niezawodność każdej zmiennej.

Na przykład większość nazwy zasobów nie może zaczynać się od cyfry, ale funkcję unikatowego ciągu może zwrócić ciąg, który rozpoczyna się od cyfry. Tak Jeśli używasz pierwotne unikatowy ciąg w danych wyjściowych, wdrożenia zakończy się niepowodzeniem. 

Można znaleźć dodatkowe informacje na temat reguł nazewnictwa zasobów i ograniczenia w [w tym artykule](https://docs.microsoft.com/azure/guidance/guidance-naming-conventions).

### <a name="deployment-location"></a>Lokalizacja wdrożenia

Możesz udostępnić możesz wersji testowej w różnych regionach platformy Azure. Chodzi o to, aby zezwolić użytkownikowi na pobranie najbliższego regionu, aby zapewnić komfort beast.

Gdy wersji testowej tworzy wystąpienie laboratorium, zawsze tworzy grupę zasobów w wybranego regionu przez użytkownika, a następnie wykonuje Szablon wdrożenia, w tym kontekście grupy. Tak szablon powinien wybierz lokalizację wdrażania z grupy zasobów:

```json
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

A następnie użyć tej lokalizacji dla każdego zasobu dla konkretnego wystąpienia laboratorium:

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

Należy się upewnić, że Twoja subskrypcja będzie mógł wdrożyć wszystkie zasoby, które mają zostać wdrożone w każdym z regionów, który wybierasz. Również należy upewnić się, że obrazów maszyn wirtualnych są dostępne we wszystkich regionach, którą chcesz włączyć, w przeciwnym razie szablonu wdrożenia nie będą działać w niektórych regionach.

### <a name="outputs"></a>Dane wyjściowe

Zwykle przy użyciu szablonów usługi Resource Manager, można wdrożyć bez wytwarzania żadnych danych wyjściowych. Jest to spowodowane znasz wartości używanych do wypełniania parametrów szablonu i możesz zawsze ręcznie sprawdzić właściwości dowolnego zasobu.

Dla szablonów Menedżera zasobów testowych z dysku, jego\'ważne powrócić do wersji testowej wszystkie informacje, który jest wymagany w celu uzyskania dostępu do laboratorium (identyfikatory URI witryny sieci Web, maszyny wirtualnej nazwy hosta, nazwy użytkownika i hasła). Upewnij się, że wszystkie nazwy wyjściowego można odczytać, ponieważ te zmienne są prezentowane z klientem.

Nie ma ograniczeń wszelkie związane z danych wyjściowych szablonu. Po prostu należy pamiętać, że wersja testowa konwertuje wszystkie wartości w danych wyjściowych **ciągi**, więc Jeśli wyślesz obiekt danych wyjściowych, użytkownik będzie widział JSON ciąg.

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

I jeszcze jedno należy wziąć pod uwagę to subskrypcja oraz limity usługi. Na przykład jeśli chcesz wdrożyć maszyny wirtualne z maksymalnie dziesięć 4-rdzeniową, musisz upewnij się, że subskrypcja używać dla swojego laboratorium umożliwia 40 rdzeni.

Można znaleźć więcej informacji na temat subskrypcji platformy Azure i limity usługi w [w tym artykule](https://docs.microsoft.com/azure/azure-subscription-service-limits). Jak wiele wersji testowych mogą być podejmowane w tym samym czasie, sprawdź, czy Twoja subskrypcja może obsługiwać \# rdzeni pomnożoną przez łączną liczbę równoczesnych testu dyski, które mogą być podejmowane.

### <a name="what-to-upload"></a>Co do przekazania

Testuj dysku usługi Resource Manager szablon jest przekazywany jako plik zip, który może zawierać różne artefaktów wdrożenia, ale musi mieć jeden plik o nazwie **main template.json**. Ten plik znajduje się szablon wdrożenia usługi Azure Resource Manager i używa go do utworzenia wystąpienia laboratorium wersji testowej.

Jeśli masz dodatkowe zasoby poza ten plik jako zasób zewnętrzny wewnątrz szablonu można do niego odwołują, lub może zawierać zasobu w pliku zip.

Podczas publikowania certyfikacji wersji testowej unzips pakietu wdrożenia i umieszcza jego zawartość z wewnętrznego kontenera obiektów blob wersji testowej. Struktury kontenerów uwzględnić strukturę pakietu wdrażania:

| Package.zip                       | Kontener obiektów blob dysku testu         |
|---|---|
| main-template.json                | https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json  |
| Templates/Solution.JSON           | https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json |
| scripts/warmup.ps1                | protokół https:\//\<\.... \>.blob.core.windows.net/\<\.... \>/scripts/warmup.ps1  |


Nazywamy identyfikatora Uri ten kontener obiektów blob podstawowy identyfikator Uri. Każdej wersji środowiska laboratoryjnego ma swój własny kontener obiektów blob, a w związku z tym, co wersja środowiska laboratoryjnego ma własny podstawowy identyfikator Uri. Wersja testowa można przekazać podstawowy identyfikator Uri zestawu pakietu wdrażania rozpakowany do szablonu za pomocą parametrów szablonu.

## <a name="transforming-template-examples-for-test-drive"></a>Przekształcanie przykłady szablonów dla wersji testowej

Proces włączenie architekturę zasobów do szablonu Menedżera zasobów testowych z dysku może być czasochłonnym zadaniem. Aby ułatwić ten proces, firma Microsoft\'wprowadzono przykłady do optymalnego [Przekształcanie bieżące szablony wdrożenia w tym miejscu](./transforming-examples-for-test-drive.md).

## <a name="how-to-publish-a-test-drive"></a>Jak opublikować wersję testową

Teraz, gdy masz utworzone wersji testowej, w tej sekcji przedstawiono wszystkie pola wymagane opublikowanie wersji testowej.

![Włączanie wersji testowej w interfejsie użytkownika](./media/azure-resource-manager-test-drive/howtopub1.png)

Pole pierwszy i najważniejszy jest do wyświetlania i ukrywania ma wersję testową włączony oferty, czy nie. Po wybraniu **tak,** pozostałej części formularza wszystkie wymagane pola są prezentowane służących do wypełniania. Po wybraniu **nie** formularza zostanie wyłączona, a Jeśli publikujesz za pomocą wersji testowej wyłączone wersji testowej zostanie usunięta ze środowiska produkcyjnego.

Uwaga: Jeśli istnieją, wszystkie testy dyski są aktywnie używane przez użytkowników, tych wersji testowych będzie kontynuował działanie aż do wygaśnięcia ich sesji.

### <a name="details"></a>Szczegóły

Następnej sekcji, aby wypełnić to oferują szczegółowe informacje o wersji testowej.

![Wersja testowa szczegółowe informacje](./media/azure-resource-manager-test-drive/howtopub2.png)

**Opis —** *wymagane* jest to, gdzie wpisać opis główny o nowościach w wersji testowej. Klient będzie przejdź tutaj, aby odczytać scenariusze, jakie wersji testowej będzie obejmował dotyczących produktu. 

**Podręcznik użytkownika -** *wymagane* to szczegółowe wskazówki dotyczące środowiska wersji testowej. Spowoduje to otwarcie siedziba klienta i zapoznać się z dokładnie co chcesz zrobić w całym ich wersji testowej. Jest ważne, że ta zawartość jest łatwy do zrozumienia i postępuj zgodnie z! (Musi być plikiem PDF)

**Testowanie dysku demonstracyjny materiał wideo —** *zalecane* podobny do podręcznika użytkownika, najlepiej jest uwzględnienie Samouczek wideo dla środowiska wersji testowej. Klient będzie oczekiwał w tym wcześniejsze lub w trakcie ich wersji testowej i zapoznać się z dokładnie co chcesz zrobić w całym ich wersji testowej. Jest ważne, że ta zawartość jest łatwy do zrozumienia i postępuj zgodnie z!

- **Nazwa** — tytuł filmu wideo
- **Link** -musi być osadzony adres URL z rury lub wideo. Przykład wprowadzenie adresu url osadzonego znajduje się poniżej:
- **Miniatura** -musi być obrazem o wysokiej jakości pikseli (533 x 324). Zalecane jest wykonanie zrzutu część środowiska wersji testowej w tym miejscu.

Poniżej przedstawiono, jak te pola wyświetlane dla klientów podczas ich wersji testowej.

![Lokalizacja pola wersji testowej w ramach oferty z portalu Marketplace](./media/azure-resource-manager-test-drive/howtopub4.png)

### <a name="technical-configuration"></a>Pomoc konfiguracji

Następnej sekcji, aby wypełnić jest którym Przekaż szablon Menedżera zasobów testowych z dysku i określ sposób specjalnie wersji testowej wystąpienia pracy.

![](./media/azure-resource-manager-test-drive/howtopub5.png)

**Wystąpienia —** *wymagane* jest to, gdzie skonfiguruj liczbę wystąpień ma, w jakie regiony i jak szybko klienci mogą uzyskać wersję testową.

- **Wystąpienia** — wybierz regionach jest, gdzie możesz wybrać wdrożonym szablonu Menedżera zasobów testowych z dysku w. Zalecane jest wystarczy wybrać jeden region, w których najczęściej spodziewasz się klientom znajdować się w lokalizacji.
- **Gorąca** -wystąpień z numer dysku testów, które są już wdrożone i Oczekiwanie na dostęp do wybranego regionu. Klienci mogą uzyskać dostęp natychmiast wersji testowych, a nie musisz czekać, aż do wdrożenia. Jego wadą jest to, że te wystąpienia są zawsze uruchamiane na Twojej subskrypcji platformy Azure, spowoduje naliczenie opłaty za większych przestojów, koszt. Zdecydowanie zaleca się mieć **co najmniej jedno wystąpienie gorąca**, większość klientów nie chcesz czekać na pełną wdrożeń zakończyć i dlatego nadania w użycia przez klientów.
- **Ciepło** — numer dysku testu wystąpień na regiony, które zostały wdrożone, a następnie maszyna wirtualna została zatrzymana i przechowywane w usłudze Azure storage. Czas oczekiwania na ciepło wystąpień jest mniejsza niż gorąca wystąpień, ale koszt magazynu na czas działania jest również mniej kosztowne.
- **Zimnych** — numer dysku testu wystąpień na region, który prawdopodobnie może być wdrożony. Wystąpienia zimnych wymagają całego szablonu Menedżera zasobów testowych z dysku przechodzić przez wdrożenie w tym czasie klient żąda wersji testowej, więc jest wolniejszy niż gorąca lub dostępu do ciepłych wystąpień. Kosztem jest jednak, że masz płacisz za czas trwania wersji testowej.

W tej chwili oblicza łączną liczbę potencjalnych współbieżnych wersji testowych ma udostępnić i sprawdzić, czy limit przydziału dla Twojej subskrypcji mogą obsługiwać współbieżnych takimi problemami znacznie mniej:

**(Liczba regionów wybrane x wystąpień gorąca) + (liczba z regionów wybrane x wystąpień bez wyłączania zasilania) + (liczba z regionów wybrane x zimnych wystąpień)**

**Dysk czas trwania testu (godziny) —** *wymagane* czas trwania ile wersji testowej pozostaną aktywne w \# godzin. Wersji testowej kończy się automatycznie po zakończeniu tego okresu.

**Test szablonu dysku usługi Resource Manager —** *wymagane* przekazywanie szablonie usługi Resource Manager. Jest to plik utworzony w poprzedniej sekcji powyżej. Nadaj nazwę pliku głównego szablonu: "main-template.json" i upewnij się, że szablon usługi Resource Manager zawiera parametry wyjściowe klucza zmiennych, które są wymagane. (Musi być plikiem .zip)

**Dostęp do informacji -** *wymagane* po klient pobiera ich wersji testowej, uzyskiwanie dostępu do informacji są prezentowane do nich. Te instrukcje są przeznaczone do udostępniania parametry wyjściowe przydatne szablonu Menedżera zasobów testowych z dysku. Aby uwzględnić parametry wyjściowe, należy użyć podwójnego nawiasów klamrowych (na przykład **{{outputname}}**), a ich zostanie wstawiony poprawnie w lokalizacji. (Formatowanie ciągu HTML zaleca się tutaj do renderowania frontonu).

### <a name="test-drive-deployment-subscription-details"></a>Szczegóły subskrypcji wdrożenia dysku testu

Sekcji końcowej, aby wypełnić ma mieć możliwość wdrażania wersji testowych automatycznie, łącząc subskrypcji platformy Azure i usługi Azure Active Directory (AD).

![Szczegóły subskrypcji wdrożenia dysku testu](./media/azure-resource-manager-test-drive/subdetails1.png)

**Identyfikator subskrypcji platformy Azure —** *wymagane* spowoduje to przydzielenie dostępu do usług platformy Azure i witryny Azure portal. Subskrypcja jest, gdzie raportowania użycia zasobów i usługi są rozliczane. Jeśli nie masz jeszcze **oddzielnych** subskrypcji platformy Azure dla wersji testowych, tylko Przejdź dalej i utworzyć. Identyfikatory subskrypcji platformy Azure można znaleźć, logując się do witryny Azure portal i przechodząc do subskrypcji, w menu po lewej stronie. (Przykład: "a83645ac-1234-5ab6-6789-1h234g764ghty")

![Subskrypcje platformy Azure](./media/azure-resource-manager-test-drive/subdetails2.png)

**Identyfikator dzierżawy usługi Azure AD —** *wymagane* Jeśli masz już dostępną identyfikator dzierżawy go poniżej można znaleźć we właściwościach -\> identyfikator katalogu.

![Właściwości usługi Active Directory platformy Azure](./media/azure-resource-manager-test-drive/subdetails3.png)

W przeciwnym razie należy utworzyć nową dzierżawę usługi Azure Active Directory.

![Wykaz usługi Azure Active Directory dzierżawy](./media/azure-resource-manager-test-drive/subdetails4.png)

![Zdefiniuj organizacji, domeny i kraju dla dzierżawy usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails5.png)

![Potwierdź wybór](./media/azure-resource-manager-test-drive/subdetails6.png)

**Identyfikator aplikacji w usłudze Azure AD —** *wymagane* następnym krokiem jest tworzenie i rejestrowanie nowej aplikacji. Ta aplikacja będzie służy do wykonywania operacji w ramach wystąpienia wersji testowej.

1. Przejdź do katalogu nowo utworzonego lub już istniejących w katalogu, a następnie wybierz pozycję usługi Azure Active directory w okienku filtrów.
2. Wyszukaj "Rejestracje aplikacji" i kliknij przycisk "Dodaj"
3. Podaj nazwę aplikacji.
4. Wybierz typ jako "aplikacja sieci Web / interfejs API"
5. Wprowadzić dowolną wartość w polu adres URL logowania, zdobyliśmy\'t można za pomocą tego pola.
6. Kliknij przycisk Utwórz.
7. Po utworzeniu aplikacji przejdź do właściwości -\> ustaw ją jako wielodostępne, a następnie kliknij przycisk Zapisz.

Kliknij pozycję Zapisz. Ostatnim krokiem jest identyfikator aplikacji dla tej aplikacji zarejestrowanych i wklej go w tym polu wersji testowej.

![Szczegółów identyfikator aplikacji w usłudze Azure AD](./media/azure-resource-manager-test-drive/subdetails7.png)

Biorąc pod uwagę używamy aplikacji do wdrożenia do subskrypcji, musimy dodać aplikację jako współautora w subskrypcji. Instrukcje te są jak poniżej:

1. Przejdź do bloku subskrypcje i wybierz odpowiednią subskrypcję, którego używasz do testowej.
1. Kliknij przycisk **kontrola dostępu (IAM)**.
1. Kliknij przycisk **przypisań ról** kartę.  ![Dodaj nową jednostkę kontroli dostępu](./media/azure-resource-manager-test-drive/SetupSub7_1.jpg)
1. Kliknij przycisk **Dodaj przypisanie roli**.
1. Ustaw rolę **Współautor**.
1. Wpisz nazwę aplikacji usługi Azure AD, a następnie wybierz aplikację, aby przypisać rolę.
    ![Dodaj uprawnienia](./media/azure-resource-manager-test-drive/SetupSub7_2.jpg)
1. Kliknij pozycję **Zapisz**.

**Klucz aplikacji w usłudze Azure AD —** *wymagane* końcowego pole jest do generowania klucza uwierzytelniania. W obszarze klucze Dodaj opis klucza, ustaw czas nigdy nie wygasa, następnie wybierz pozycję Zapisz. Jest **ważne** pozwala uniknąć wygasłe klucza, co spowoduje przerwanie wersji testowej w środowisku produkcyjnym. Skopiuj tę wartość i wklej go do wymagane pole wersji testowej.

![Pokazuje kluczy dla aplikacji usługi Azure AD](./media/azure-resource-manager-test-drive/subdetails8.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wszystkich pól wersji testowej wypełnione przejść i **ponownie opublikować** oferty. Po upływie wersji testowej certyfikacji powinien przeprowadzić dokładnie przetestować środowiska klienta w **(wersja zapoznawcza)** oferty. Uruchom wersję testową w interfejsie użytkownika, a następnie otwórz Twojej subskrypcji platformy Azure w witrynie Azure portal i sprawdź, czy wersji testowych są w pełni wdrażany prawidłowo.

![Azure Portal](./media/azure-resource-manager-test-drive/subdetails9.png)

Jest to należy pamiętać, że nie zostaną usunięte wszystkie wystąpienia wersji testowej jako są udostępnione dla klientów, więc wersji testowej usługi będzie automatycznie wyczyścić te grupy zasobów po zakończeniu z nim pracy klienta.

Gdy masz doświadczenie z ofertą użytkownika (wersja zapoznawcza), nadszedł czas na **emisji na żywo**! Gdy oferta została publikowanych w podwójnego wyboru całe środowisko typu end to end jest proces ostateczny Przegląd, od firmy Microsoft. Jeśli z jakiegoś powodu odrzucenia pobiera oferty firma Microsoft będzie wysyłał powiadomienie do inżynierów skontaktuj się z ofertą wyjaśniający, co będzie potrzebne do rozwiązania.

Jeśli masz więcej pytań, czego szukasz poradę dotyczącą rozwiązywania problemów lub mają być łatwiejsza, wersji testowej, przejdź do [— często zadawane pytania, rozwiązywanie problemów i najlepsze rozwiązania](./marketing-and-best-practices.md).
