---
title: Jak zainstalować HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak zainstalować HANA na platformie SAP HANA na platformie Azure (duże wystąpienie).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76a7ce99799b85d81aa6e127ebe1e57e2df3e59a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164742"
---
# <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>Przykład instalacji oprogramowania SAP HANA w dużych wystąpieniach HANA

W tej sekcji pokazano, jak instalowanie platformy SAP HANA w jednostce dużych wystąpień HANA. Stan początkowy, firma Microsoft ma następującą postać:

- Microsoft jest udostępniane wszystkie dane, aby wdrożyć duże wystąpienie SAP HANA.
- Duże wystąpienie SAP HANA jest otrzymanych od firmy Microsoft.
- Utworzono sieć wirtualną platformy Azure, która jest połączona z siecią lokalną.
- Masz połączenie obwodu ExpressRotue dla dużych wystąpień HANA do tej samej sieci wirtualnej platformy Azure.
- Możesz zainstalować Maszynie wirtualnej platformy Azure są używane jako przesiadkowym dużych wystąpień HANA.
- Możesz upewnienie się, że można połączyć z przesiadkowym jednostkowy dużych wystąpień HANA i na odwrót.
- Sprawdzane, czy zainstalować wszystkie niezbędne pakiety i poprawki.
- Przeczytaj uwagi SAP i dokumentacje dotyczącą dotyczące instalacji platformy HANA w systemie operacyjnym, używania i upewnienie się, że wersja platformy HANA wybór jest obsługiwany w systemie operacyjnym wydania.

Przedstawiono na następnym sekwencji jest pobieranie pakiety instalacyjne oprogramowania HANA w celu przesiadkowym maszyny Wirtualnej, w tym przypadku systemem operacyjnym Windows, kopiowania pakietów do jednostki dużych wystąpień HANA i sekwencji instalacji.

## <a name="download-of-the-sap-hana-installation-bits"></a>Pobieranie usługi bits instalacji oprogramowania SAP HANA
Ponieważ jednostki dużych wystąpień HANA nie mają bezpośredniego połączenia z Internetem, możesz nie można bezpośrednio pobrać pakiety instalacyjne programu SAP do maszyny Wirtualnej platformy HANA duże wystąpienia. Aby przezwyciężyć Brak bezpośrednie połączenie z Internetem, należy przesiadkowym. Możesz pobrać pakiety w celu przesiadkowym maszyny Wirtualnej.

Aby pobrać pakiety instalacyjne oprogramowania HANA, konieczne będzie SAP S-użytkownik lub inny użytkownik, który umożliwia dostęp do portalu Marketplace SAP. Po zalogowaniu się w przechodzą ta sekwencja ekrany:

Przejdź do [SAP Service Marketplace](https://support.sap.com/en/index.html) > kliknij przycisk pobierania oprogramowania > instalacje i uaktualnienia > przez indeks alfabetyczny > w obszarze H — SAP HANA Edition platformy > oprogramowania SAP HANA platformy Edition w wersji 2.0 > instalacji > Pobierz następujące pliki

![Pobierz HANA instalacji](./media/hana-installation/image16_download_hana.PNG)

W przypadku demonstracji pobraliśmy pakietów instalacyjnych SAP HANA w wersji 2.0. Na serwerze Azure przesiadkowym maszyny Wirtualnej należy rozwinąć samowyodrębniający archiwa do katalogu jak pokazano poniżej.

![Wyodrębnij HANA instalacji](./media/hana-installation/image17_extract_hana.PNG)

Archiwum są wyodrębniane, Kopiuj Katalog utworzony przez wyodrębniania, w tym przypadku powyżej 51052030, HANA, duże wystąpienia jednostki do woluminu /hana/shared do katalogu, który został utworzony.

> [!Important]
> Nie należy kopiować pakiety instalacyjne do katalogu głównego lub rozruchu jednostki LUN, ponieważ miejsce jest ograniczone i musi zostać użyte przez inne procesy, jak również.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalowanie platformy SAP HANA w jednostce dużych wystąpień HANA
Aby zainstalować oprogramowanie SAP HANA, należy zalogować się jako użytkownik główny. Tylko główny ma wystarczających uprawnień, aby zainstalować oprogramowanie SAP HANA.
Pierwszą rzeczą, jaką należy wykonać, jest ustawić uprawnienia dla katalogu w którym zostały skopiowane przez/hana/udostępnione. Uprawnienia muszą ustawić np.

```
chmod –R 744 <Installation bits folder>
```

Jeśli chcesz zainstalować oprogramowanie SAP HANA przy użyciu konfiguracji graficznej pakietu gtk2 należy zainstalować w dużych wystąpieniach HANA. Sprawdź, czy jest ona zainstalowana za pomocą polecenia

```
rpm –qa | grep gtk2
```

W dalszych krokach będziemy są ukazujące instalacji oprogramowania SAP HANA przy użyciu graficznego interfejsu użytkownika. Przejdź do katalogu instalacji i przejdź do podkatalogu HDB_LCM_LINUX_X86_64 następnego kroku. Uruchamianie

```
./hdblcmgui 
```
z tego katalogu. Teraz wprowadzenie zapoznasz się sekwencji ekranów, gdzie należy podać dane dotyczące instalacji. W przypadku wykazać firma Microsoft jest instalowany serwer bazy danych SAP HANA i składniki klienta SAP HANA. W związku z tym nasze zaznaczenie jest "Baza danych SAP HANA", jak pokazano poniżej

![Wybierz platformy HANA w instalacji](./media/hana-installation/image18_hana_selection.PNG)

Na następnym ekranie możesz wybrać opcję "Zainstalować nowy System"

![Wybierz platformy HANA nowa instalacja](./media/hana-installation/image19_select_new.PNG)

Po wykonaniu tego kroku należy wybrać między kilka dodatkowych składników, które mogą być instalowane dodatkowo na serwerze bazy danych SAP HANA.

![Wybierz dodatkowe składniki platformy HANA](./media/hana-installation/image20_select_components.PNG)

Na potrzeby niniejszej dokumentacji Wybraliśmy klienckie HANA SAP i SAP HANA Studio. Instalowany jest także wystąpienie skalowanie w górę. Dlatego na następnym ekranie, musisz wybrać "System jednego hosta" 

![Wybierz opcję instalacji skalowanie w górę](./media/hana-installation/image21_single_host.PNG)

Na następnym ekranie należy podać dane

![Podaj identyfikator SID programu SAP HANA](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako identyfikator systemu HANA (SID), należy podać ten sam identyfikator SID jako podane firmy Microsoft, gdy uporządkowane wdrażanie dużych wystąpień HANA. Wybierając inny identyfikator SID sprawia, że instalacja się nie powieść z powodu problemów z uprawnieniami dostępu na różnych woluminach

Jako instalację katalogu, możesz użyć/hana/udostępnionego katalogu. W następnym kroku należy podać lokalizacje plików danych HANA i pliki dziennika HANA


![Podaj lokalizację dziennika HANA](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Należy zdefiniować jako dane i woluminy, które już dołączone punkty instalacji, które zawierają SID w zaznaczenie ekranu przed tym ekranie wybrano plików dziennika. Jeśli identyfikator SID niezgodność, który został wpisany, na ekranie przed wróć i dopasuj identyfikator SID wartość, na punkty instalacji.

W następnym kroku Sprawdź nazwę hosta, a ostatecznie Popraw go. 

![Nazwa hosta przeglądu](./media/hana-installation/image24_review_host_name.PNG)

W następnym kroku, należy również pobierać dane, które należy nadać do firmy Microsoft uporządkowane wdrażanie dużych wystąpień HANA. 

![Podaj użytkownika systemowego identyfikatorów UID i GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Należy podać ten sam identyfikator użytkownika systemu i Identyfikatora grupy użytkowników jako podane firmy Microsoft, jak kolejność wdrażania jednostki. Nie można nadać bardzo takich samych identyfikatorów, instalacja oprogramowania SAP HANA w jednostce dużych wystąpień HANA kończy się niepowodzeniem.

W dwóch następnych ekrany, które firma Microsoft nie są wyświetlane w tej dokumentacji, należy podać hasło dla użytkownika SYSTEM bazy danych SAP HANA i hasło dla użytkownika sapadm, używany dla agenta hosta SAP, który pobiera instalowany jako część datab platformy SAP HANA wystąpienie środowiska ASE.

Po zdefiniowaniu hasła, ekran potwierdzenia są wyświetlane. Sprawdź wszystkie dane na liście i kontynuować instalację. Osiągniesz ekran pokazujący postęp, która dokumentuje postęp instalacji, jak ta, poniżej

![Sprawdzanie postępu instalacji](./media/hana-installation/image27_show_progress.PNG)

Po zakończeniu instalacji, należy dodać obraz podobny do następującego

![Instalacja została zakończona.](./media/hana-installation/image28_install_finished.PNG)

W tym momencie wystąpienie SAP HANA, powinny być nawet i działa i gotowy do użycia. Powinien móc połączyć je z SAP HANA Studio. Upewnij się również wyszukać najnowszych poprawek oprogramowanie SAP HANA, a następnie zastosować te poprawki.


**Następne kroki**

- Zapoznaj się [duże wystąpienia SAP HANA wysoką dostępność i odzyskiwanie awaryjne na platformie Azure](hana-overview-high-availability-disaster-recovery.md).

