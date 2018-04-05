---
title: Zarządzanie usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage
description: Dowiedz się, jak zarządzać usługą Azure Cosmos DB w Eksploratorze usługi Azure Storage.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Omówienie przewodnika rozwiązywania problemów z aplikacją Azure Cosmos DB w Eksploratorze usługi Storage

[Azure Cosmos DB w Eksploratorze usługi Azure Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) jest aplikacją autonomiczną, która umożliwia nawiązanie połączenia z kontami usługi Azure Cosmos DB hostowanymi na platformie Azure i suwerennych chmurach w systemie Windows, macOS lub Linux. Umożliwia ona zarządzanie jednostkami usługi Azure Cosmos DB, wykonywanie operacji na danych oraz aktualizowanie procedur składowanych i wyzwalaczy, a także innych jednostek platformy Azure, takich jak obiekty blob i kolejki usługi Storage.

Ten przewodnik zawiera podsumowanie rozwiązań typowych problemów występujących dla aplikacji Azure Cosmos DB w Eksploratorze usługi Storage.

- [Problemy z logowaniem](#Sign-in-issues)
  - [Certyfikat z podpisem własnym w łańcuchu certyfikatów](#Self-signed-certificate-in-certificate-chain)
  - [Brak możliwości pobrania subskrypcji](#Unable-to-retrieve-subscriptions)
  - [Brak możliwości wyświetlenia strony uwierzytelniania](#Unable-to-see-the-authentication-page)
  - [Nie można usunąć konta](#Cannot-remove-account)
- [Problem z serwerem proxy HTTP/HTTPS](#Http/Https-proxy-issue)
- [Problem z węzłem „Programowanie” w węźle „Lokalne i dołączone”](#Development-node-under-Local-and-Attached-node-issue)
- [Błąd dołączania konta usługi Azure Cosmos DB w węźle „Lokalne i dołączone”](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Błąd rozwijania węzła usługi Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Następne kroki](#Next-steps)

<h2 id="Sign-in-issues">Problemy z logowaniem</h2>

Przed kontynuacją spróbuj ponownie uruchomić aplikację i zobacz, czy problemy mogą zostać usunięte.

<h2 id="Self-signed-certificate-in-certificate-chain">Certyfikat z podpisem własnym w łańcuchu certyfikatów</h2>

Istnieje kilka przyczyn wyświetlenia tego błędu, przy czym dwoma najbardziej typowymi są:

1. Znajdujesz się za „przezroczystym serwerem proxy”, co oznacza, że ktoś (na przykład dział IT) przechwytuje ruch HTTPS, odszyfrowuje go, a następnie szyfruje za pomocą certyfikatu z podpisem własnym.

2. Używasz oprogramowania, takiego jak oprogramowanie antywirusowe, które wprowadza certyfikaty SSL z podpisem własnym do wiadomości protokołu HTTPS, które otrzymujesz.

Gdy Eksplorator usługi Storage napotka jeden z tych „certyfikatów z podpisem własnym”, nie będzie już wiedział, czy odbierany komunikat HTTPS został naruszony. Jeśli jednak masz kopię certyfikatu z podpisem własnym, możesz poinformować Eksploratora usługi Storage, aby mu zaufał. Jeśli nie masz pewności, kto wprowadza certyfikat, możesz spróbować go znaleźć samodzielnie, wykonując następujące czynności:

1. Zainstaluj protokół Open SSL
     - [System Windows](https://slproweb.com/products/Win32OpenSSL.html) (dowolna z wersji uproszczonych jest OK)
     - Komputery Mac i system Linux: powinien być dołączony do systemu operacyjnego
2. Uruchom protokół Open SSL
    - System Windows: przejdź do katalogu instalacyjnego, a następnie **/bin/**, po czym kliknij dwukrotnie plik **openssl.exe**.
    - Komputery Mac i system Linux: wykonaj polecenie **openssl** z terminala
3. Wykonaj polecenie `s_client -showcerts -connect microsoft.com:443`
4. Wyszukaj certyfikaty z podpisem własnym. Jeśli nie wiesz, które z nich są z podpisem własnym, wówczas poszukaj pozycji, gdzie podmiot („s:”) i wystawca („i:”) są identyczni.
5.  Po znalezieniu jakichkolwiek certyfikatów z podpisem własnym skopiuj i wklej wszystko, poczynając od **---BEGIN CERTIFICATE---** do **---END CERTIFICATE---**, do nowego pliku cer dla każdego z nich.
6.  Otwórz Eksploratora usługi Storage, a następnie przejdź do pozycji **Edytuj** > **Certyfikaty SSL** > **Importuj certyfikaty**. Za pomocą selektora plików znajdź, wybierz i otwórz utworzony plik cer.

Jeśli nie możesz odnaleźć żadnych certyfikatów z podpisem własnym za pomocą powyższych kroków, możesz wysłać opinię, aby uzyskać dalszą pomoc.

<h2 id="Unable-to-retrieve-subscriptions">Brak możliwości pobrania subskrypcji</h2>

Jeśli nie możesz pobrać subskrypcji po pomyślnym zalogowaniu:

- Sprawdź, czy Twoje konto użytkownika ma dostęp do subskrypcji, logując się do witryny [Azure Portal](http://portal.azure.com/)
- Upewnij się, że podczas logowania używasz poprawnego środowiska ([Azure](http://portal.azure.com/), [Azure — Chiny](https://portal.azure.cn/), [Azure — Niemcy](https://portal.microsoftazure.de/), [Azure — instytucje rządowe USA](http://portal.azure.us/) lub niestandardowe środowisko/usługa Azure Stack)
- Jeśli znajdujesz się za serwerem proxy, upewnij się, że masz poprawnie skonfigurowany serwer proxy Eksploratora usługi Storage
- Spróbuj usunąć i ponownie dodać konto
- Spróbuj usunąć następujące pliki z katalogu macierzystego (takiego jak: C:\Users\ContosoUser), a następnie ponownie dodać konto:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Podczas logowania obejrzyj wszystkie komunikaty o błędach na konsoli narzędzi dla deweloperów (f12)

![console](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">Nie można wyświetlić strony uwierzytelniania</h2>  

Jeśli nie możesz wyświetlić strony uwierzytelniania:

- W zależności od prędkości połączenia załadowanie strony logowania może potrwać trochę czasu, więc poczekaj co najmniej jedną minutę przed zamknięciem okna dialogowego uwierzytelniania
- Jeśli znajdujesz się za serwerem proxy, upewnij się, że masz poprawnie skonfigurowany serwer proxy Eksploratora usługi Storage
- Wywołaj konsolę dla deweloperów, naciskając klawisz F12. Obejrzyj odpowiedzi z konsoli dla deweloperów i zobacz, czy możesz znaleźć jakieś wskazówki dotyczące tego, dlaczego uwierzytelnianie nie działa

<h2 id="Cannot-remove-account">Nie można usunąć konta</h2>

Jeśli nie możesz usunąć konta lub jeśli link do ponownego uwierzytelniania nie działa

- Spróbuj usunąć następujące pliki ze swojego katalogu macierzystego, a następnie ponownie dodaj konto:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Jeśli chcesz usunąć zasoby usługi Storage dołączone do systemu SAS, usuń:
  - Folder %AppData%/StorageExplorer dla systemu Windows
  - /Users/<Twoja_nazwa>/Library/Application Support/StorageExplorer dla komputerów Mac
  - ~/.config/StorageExplorer dla systemu Linux
  - Po usunięciu tych plików **trzeba będzie ponownie wprowadzić wszystkie poświadczenia**


<h2 id="Http/Https-proxy-issue">Problem z serwerem proxy HTTP/HTTPS</h2>

Podczas konfigurowania serwera proxy HTTP/HTTPS w środowisku ASE nie możesz wyświetlać listy węzłów usługi Azure Cosmos DB w drzewie po lewej stronie. Jest to znany problem i zostanie rozwiązany w następnej wersji. Jako obejścia w tej chwili możesz użyć eksploratora danych usługi Azure Cosmos DB w witrynie Azure Portal. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problem z węzłem „Programowanie” w węźle „Lokalne i dołączone”</h2>

Nie ma odpowiedzi po kliknięciu węzła „Programowanie” w węźle „Lokalne i dołączone” drzewa po lewej stronie.  Zachowanie jest oczekiwane. Emulator lokalny usługi Azure Cosmos DB będzie obsługiwany w następnej wersji.

![Węzeł projektowania](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Błąd dołączania konta usługi Azure Cosmos DB w węźle „Lokalne i dołączone”</h2>

Jeśli poniższy błąd zostanie wyświetlony po dołączeniu konta usługi Azure Cosmos DB w węźle „Lokalne i dołączone”, sprawdź, czy używasz właściwych parametrów połączenia.

![Błąd dołączania usługi Azure Cosmos DB w węźle Lokalne i dołączone](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Błąd rozwijania węzła usługi Azure Cosmos DB</h2>

Podczas próby rozwinięcia węzłów drzewa z lewej strony możesz zobaczyć poniższy błąd. 

![Błąd rozwijania](./media/troubleshoot-cosmosdb/expand-error.png)

Spróbuj wykonać poniższe sugestie:

- Sprawdź, czy konto usługi Azure Cosmos DB nie jest aktualnie na etapie aprowizacji, a następnie ponów próbę po pomyślnym utworzeniu konta.
- Jeśli konto znajduje się w węźle „Szybki dostęp” lub węzłach „Lokalne i dołączone”, sprawdź, czy konto zostało usunięte. Jeśli tak, należy ręcznie usunąć węzeł.

<h2 id="Next-steps">Następne kroki</h2>

Jeśli te rozwiązania nie działają w Twoim przypadku, wyślij wiadomość e-mail do zespołu narzędzi dla deweloperów usługi Cosmos Azure DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) zawierającą szczegółowe informacje o problemach w celu ich rozwiązania.





