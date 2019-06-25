---
title: Jak zainstalować HANA na platformie SAP HANA na platformie Azure (duże wystąpienia) | Dokumentacja firmy Microsoft
description: Jak zainstalować HANA na platformie SAP HANA na platformie Azure (duże wystąpienia).
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
ms.openlocfilehash: f4629894933507bda7359fb034c4079d38100029
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203882"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalowanie oprogramowania HANA na platformie SAP HANA na platformie Azure (duże wystąpienia)

Aby zainstalować HANA na platformie SAP HANA na platformie Azure (duże wystąpienia), najpierw wykonaj następujące czynności:
- Microsoft zapewnia wszystkie dane w celu wdrożenia dla Ciebie na duże wystąpienie SAP HANA.
- Duże wystąpienie SAP HANA jest otrzymywać od firmy Microsoft.
- Możesz utworzyć sieci wirtualnej platformy Azure, która jest połączona z siecią lokalną.
- Połączenie obwodu usługi ExpressRoute dla dużych wystąpień HANA z tej samej sieci wirtualnej platformy Azure.
- Należy zainstalować maszynie wirtualnej platformy Azure, którego używasz jako przesiadkowym dla dużych wystąpień HANA.
- Upewnij się, że można połączyć z przesiadkowym do urządzenia dużych wystąpień HANA i na odwrót.
- Możesz sprawdzić, czy zainstalować wszystkie niezbędne pakiety i poprawki.
- Możesz przeczytać informacje o SAP i dokumentacji dotyczącej instalacji oprogramowania HANA w systemie operacyjnym, z którego korzystasz. Upewnij się, że wersja platformy HANA wybór jest obsługiwany w wersji systemu operacyjnego.

Następna sekcja przedstawia przykład pobierania pakietów instalacyjnych HANA do maszyny wirtualnej okno przeskoku. W tym przypadku systemu operacyjnego jest Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Pobieranie danych instalacji oprogramowania SAP HANA
Jednostki dużych wystąpień HANA nie są bezpośrednio połączone z Internetem. Nie można bezpośrednio pobrać pakietów instalacyjnych programu SAP do platformy HANA, duże wystąpienie maszyny wirtualnej. Zamiast tego należy pobrać pakiety do maszyny wirtualnej okno przeskoku.

Potrzebny użytkownik S oprogramowania SAP lub inny użytkownik, który umożliwia dostęp do portalu Marketplace SAP.

1. Zaloguj się i przejdź do [SAP Service Marketplace](https://support.sap.com/en/index.html). Wybierz **Pobierz oprogramowanie** > **instalacje i uaktualnienia** > **przez indeks alfabetyczny**. Następnie wybierz pozycję **w obszarze H — SAP HANA platformy Edition** > **SAP HANA platformy w wersji 2.0** > **instalacji**. Pobierz pliki pokazano na poniższym zrzucie ekranu.

   ![Zrzut ekranu przedstawiający plików do pobrania](./media/hana-installation/image16_download_hana.PNG)

2. W tym przykładzie firma Microsoft pobrane pakiety instalacyjne SAP HANA w wersji 2.0. Na platformie Azure, przejście maszyny wirtualnej w polu, a następnie rozwiń samowyodrębniający archiwa do katalogu, jak pokazano poniżej.

   ![Zrzut ekranu przedstawiający samowyodrębniający archiwum](./media/hana-installation/image17_extract_hana.PNG)

3. Archiwum są wyodrębniane, Kopiuj Katalog utworzony przez wyodrębniania (w tym przypadku 51052030). Skopiuj katalog z woluminu /hana/shared jednostki dużych wystąpień HANA do katalogu, który został utworzony.

   > [!Important]
   > Nie należy kopiować pakiety instalacyjne do katalogu głównego lub rozruchu jednostce LUN, ponieważ miejsce jest ograniczone i musi zostać użyte przez inne procesy, jak również.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalowanie platformy SAP HANA w jednostce dużych wystąpień HANA
Aby zainstalować oprogramowanie SAP HANA, zaloguj się jako użytkownik główny. Tylko główny ma wystarczających uprawnień, aby zainstalować oprogramowanie SAP HANA. Ustaw uprawnienia katalogu w którym zostały skopiowane przez /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Jeśli chcesz zainstalować oprogramowanie SAP HANA przy użyciu ustawień interfejsu graficznego użytkownika pakietu gtk2 należy zainstalować w dużych wystąpieniach HANA. Aby sprawdzić, czy jest zainstalowany, uruchom następujące polecenie:

```
rpm –qa | grep gtk2
```

(W kolejnych krokach przedstawiono instalacji oprogramowania SAP HANA przy użyciu graficznego interfejsu użytkownika.)

Przejdź do katalogu instalacji, a następnie przejdź do podkatalogu HDB_LCM_LINUX_X86_64. 

Z tego katalogu Uruchom polecenie:

```
./hdblcmgui 
```
Co to punkt, postęp za pośrednictwem sekwencji ekranów, w których dostarczają dane do instalacji. W tym przykładzie firma Microsoft jest instalowany serwer bazy danych SAP HANA i składniki klienta SAP HANA. Dlatego jest naszym wybór **bazy danych SAP HANA**.

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekran, baza danych SAP HANA wybrane](./media/hana-installation/image18_hana_selection.PNG)

Na następnym ekranie Wybierz **zainstalować nowy System**.

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekran, zainstaluj wybrane nowego systemu](./media/hana-installation/image19_select_new.PNG)

Następnie wybierz spośród kilku dodatkowych składników, które można zainstalować.

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekran, lista dodatkowych składników](./media/hana-installation/image20_select_components.PNG)

W tym miejscu możemy wybrać klienckie HANA SAP i SAP HANA Studio. Możemy również zainstalować wystąpienie skalowanie w górę. Następnie wybierz **jednego hosta, System**. 

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekranu, z jednego systemu hosta wybrane](./media/hana-installation/image21_single_host.PNG)

Następnie należy podać dane.

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekran, pola właściwości systemowe, aby zdefiniować](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako identyfikator systemu HANA (SID), należy podać ten sam identyfikator SID jako podane firmy Microsoft, gdy uporządkowane wdrażanie dużych wystąpień HANA. Wybierając inny identyfikator SID powoduje, że instalacja nie powiedzie się, ze względu na problemy związane z uprawnieniami dostępu na różnych woluminach.

Użyj katalogu /hana/shared ścieżki instalacji. W następnym kroku podasz lokalizacje plików danych HANA i plików dziennika platformy HANA.


![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekranu, z polami obszaru danych i dziennika](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Identyfikator SID, określony podczas definiowania właściwości systemu (temu dwa ekrany) powinien odpowiadać identyfikator SID punktów instalacji. Jeśli występuje niezgodność, przejdź wstecz i dopasuj identyfikator SID wartość, na punkty instalacji.

W następnym kroku Sprawdź nazwę hosta, a ostatecznie Popraw go. 

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekranu, z nazwą hosta](./media/hana-installation/image24_review_host_name.PNG)

W następnym kroku, należy również pobierać dane, które należy nadać do firmy Microsoft uporządkowane wdrażanie dużych wystąpień HANA. 

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia, z polami administratora systemu, aby zdefiniować](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Zapewniają takie same **identyfikator użytkownika administratora systemu** i **identyfikator grupy użytkowników** zgodnie z postanowieniami w firmie Microsoft, w jak kolejność wdrażania jednostki. W przeciwnym razie instalacja oprogramowania SAP HANA w jednostce dużych wystąpień HANA kończy się niepowodzeniem.

Następne dwa ekrany nie są wyświetlane w tym miejscu. Umożliwiają one Podaj hasło dla użytkownika SYSTEM bazy danych SAP HANA i hasło dla użytkownika sapadm. Drugim jest używana do agenta hosta SAP, która pobiera zainstalowane w ramach wystąpienia bazy danych SAP HANA.

Po zdefiniowaniu hasła, zostanie wyświetlony ekran potwierdzenia. Sprawdź wszystkie dane na liście, a następnie kontynuuj instalację. Osiągniesz ekran pokazujący postęp, która dokumentuje postęp instalacji, podobny do tego:

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekran, wskaźniki postępu instalacji](./media/hana-installation/image27_show_progress.PNG)

Po zakończeniu instalacji, powinien zostać wyświetlony ekran podobny do poniższego:

![Zrzut ekranu programu SAP HANA zarządzania cyklem życia ekranu, wskazując instalacji jest ukończona.](./media/hana-installation/image28_install_finished.PNG)

Wystąpienie SAP HANA powinno być teraz nawet i uruchomiona i gotowy do użycia. Powinien móc połączyć je z SAP HANA Studio. Upewnij się również wyszukać i Zastosuj najnowsze aktualizacje.


## <a name="next-steps"></a>Kolejne kroki

- [Duże wystąpienia SAP HANA wysoką dostępność i odzyskiwanie awaryjne na platformie Azure](hana-overview-high-availability-disaster-recovery.md)

