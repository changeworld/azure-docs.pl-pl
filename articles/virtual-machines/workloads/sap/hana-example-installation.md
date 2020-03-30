---
title: Jak zainstalować hana na SAP HANA na platformie Azure (duże wystąpienia) | Dokumenty firmy Microsoft
description: Jak zainstalować hana na SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617207"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalowanie hana na SAP HANA na platformie Azure (duże wystąpienia)

Aby zainstalować hana na SAP HANA na platformie Azure (duże wystąpienia), należy najpierw wykonać następujące czynności:
- Należy podać firmie Microsoft wszystkie dane do wdrożenia dla Ciebie w sap HANA wystąpienie duże.
- Otrzymujesz wystąpienie duże SAP HANA od firmy Microsoft.
- Tworzenie sieci wirtualnej platformy Azure, która jest połączona z siecią lokalną.
- Połączenie obwodu usługi ExpressRoute dla dużych wystąpień HANA do tej samej sieci wirtualnej platformy Azure.
- Należy zainstalować maszynę wirtualną platformy Azure, która jest używana jako pole szybkiego dostępu dla dużych wystąpień HANA.
- Upewnij się, że można połączyć się z pola przesuń do jednostki dużych wystąpień HANA i na odwrót.
- Sprawdź, czy wszystkie niezbędne pakiety i poprawki są zainstalowane.
- Przeczytaj uwagi SAP i dokumentację dotyczącą instalacji HANA w używanym systemie operacyjnym. Upewnij się, że wersja HANA wyboru jest obsługiwana w wersji systemu operacyjnego.

W następnej sekcji przedstawiono przykład pobierania pakietów instalacyjnych HANA do maszyny wirtualnej pola przeskoku. W takim przypadku systemem operacyjnym jest system Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Pobierz bity instalacyjne SAP HANA
Jednostki dużych wystąpień HANA nie są bezpośrednio połączone z Internetem. Nie można bezpośrednio pobrać pakietów instalacyjnych z SAP do maszyny wirtualnej dużych wystąpień HANA. Zamiast tego można pobrać pakiety do maszyny wirtualnej pole skoku.

Potrzebujesz użytkownika SAP S lub innego użytkownika, który umożliwia dostęp do portalu SAP Marketplace.

1. Zaloguj się i przejdź do [portalu SAP Service Marketplace](https://support.sap.com/en/index.html). Wybierz pobierz instalacje **oprogramowania** > **i uaktualnij** > **według indeksu alfabetycznego**. Następnie wybierz **opcję W obszarze H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Instalacja**. Pobierz pliki pokazane na poniższym zrzucie ekranu.

   ![Zrzut ekranu przedstawiający pliki do pobrania](./media/hana-installation/image16_download_hana.PNG)

2. W tym przykładzie pobraliśmy pakiety instalacyjne SAP HANA 2.0. Na maszynie wirtualnej skrzynki szybkiego dostępu platformy Azure rozwiń archiwa samorozdłużania do katalogu, jak pokazano poniżej.

   ![Zrzut ekranu przedstawiający samorozdysmujące się archiwum](./media/hana-installation/image17_extract_hana.PNG)

3. Po wyodrębnianiu archiwów skopiuj katalog utworzony przez wyodrębnienie (w tym przypadku 51052030). Skopiuj katalog z jednostki dużych wystąpień HANA /hana/udostępniony wolumin do utworzonego katalogu.

   > [!Important]
   > Nie kopiuj pakietów instalacyjnych do głównej lub rozruchowej jednostki LUN, ponieważ ilość miejsca jest ograniczona i musi być używana również przez inne procesy.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalowanie sap hana na jednostce dużych wystąpień HANA
Aby zainstalować SAP HANA, zaloguj się jako katalog główny użytkownika. Tylko katalog główny ma wystarczające uprawnienia do zainstalowania sap HANA. Ustaw uprawnienia do katalogu skopiowanego do /hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Jeśli chcesz zainstalować SAP HANA przy użyciu konfiguracji graficznego interfejsu użytkownika, pakiet gtk2 musi być zainstalowany na dużych wystąpieniach HANA. Aby sprawdzić, czy jest zainstalowany, uruchom następujące polecenie:

```
rpm –qa | grep gtk2
```

(W późniejszych krokach pokazujemy konfigurację SAP HANA z graficznym interfejsem użytkownika).

Przejdź do katalogu instalacyjnego i przejdź do HDB_LCM_LINUX_X86_64 katalogu podrzędnego. 

Z tego katalogu uruchom:

```
./hdblcmgui 
```
W tym momencie można przejść przez sekwencję ekranów, w którym można podać dane do instalacji. W tym przykładzie instalujemy serwer bazy danych SAP HANA i składniki klienta SAP HANA. Dlatego nasz wybór to **SAP HANA Database**.

![Zrzut ekranu przedstawiający ekran Zarządzania cyklem życia SAP HANA z wybraną bazą danych SAP HANA](./media/hana-installation/image18_hana_selection.PNG)

Na następnym ekranie wybierz pozycję **Zainstaluj nowy system**.

![Zrzut ekranu przedstawiający ekran Zarządzania cyklem życia SAP HANA z wybraną wybraną wybraną wybraną instalacją nowego systemu](./media/hana-installation/image19_select_new.PNG)

Następnie wybierz spośród kilku dodatkowych składników, które można zainstalować.

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z listą dodatkowych składników](./media/hana-installation/image20_select_components.PNG)

Tutaj wybieramy klienta SAP HANA i SAP HANA Studio. Instalujemy również wystąpienie skalowania w górę. Następnie wybierz system **z jednym hostem**. 

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z wybranym systemem pojedynczego hosta](./media/hana-installation/image21_single_host.PNG)

Następnie podaj kilka danych.

![Zrzut ekranu przedstawiający ekran Zarządzania cyklem życia SAP HANA z polami właściwości systemu do zdefiniowania](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Jako identyfikator systemu HANA (SID) należy podać ten sam identyfikator SID, który został podany przez firmę Microsoft podczas zamawiania wdrożenia wystąpienia dużych hana. Wybranie innego identyfikatora SID powoduje niepowodzenie instalacji z powodu problemów z uprawnieniami dostępu do różnych woluminów.

W przypadku ścieżki instalacji należy użyć katalogu /hana/shared. W następnym kroku należy podać lokalizacje dla plików danych HANA i plików dziennika HANA.


![Zrzut ekranu przedstawiający ekran Zarządzania cyklem życia SAP HANA z polami obszaru danych i dziennika](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Identyfikator SID określony podczas definiowania właściwości systemu (dwa ekrany temu) powinien być zgodny z identyfikatorem SID punktów instalacji. Jeśli występuje niezgodność, wróć i dostosuj identyfikator SID do wartości, którą masz w punktach instalacji.

W następnym kroku przejrzyj nazwę hosta i ostatecznie ją popraw. 

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA z nazwą hosta](./media/hana-installation/image24_review_host_name.PNG)

W następnym kroku należy również pobrać dane, które zostały nadane do firmy Microsoft podczas zamawiania wdrożenia wystąpienia dużych HANA. 

![Zrzut ekranu przedstawiający zarządzanie cyklem życia SAP HANA z polami administratora systemu do zdefiniowania](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Podaj ten sam **identyfikator użytkownika administratora systemu** i identyfikator grupy **użytkowników,** co podane firmie Microsoft, podczas zamawiania wdrożenia jednostki. W przeciwnym razie instalacja sap HANA na jednostce dużych wystąpień HANA kończy się niepowodzeniem.

Następne dwa ekrany nie są wyświetlane tutaj. Umożliwiają one podanie hasła dla użytkownika systemu bazy danych SAP HANA oraz hasła użytkownika sapadm. Ten ostatni jest używany dla agenta hosta SAP, który zostanie zainstalowany jako część wystąpienia bazy danych SAP HANA.

Po zdefiniowaniu hasła zostanie wyświetlony ekran potwierdzenia. sprawdź wszystkie wymienione dane i kontynuuj instalację. Docierasz do ekranu postępu, który dokumentuje postęp instalacji, taki jak ten:

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA ze wskaźnikami postępu instalacji](./media/hana-installation/image27_show_progress.PNG)

Po zakończeniu instalacji powinien zostać wyświetlony ekran taki jak ten:

![Zrzut ekranu przedstawiający ekran zarządzania cyklem życia SAP HANA, wskazujący, że instalacja została zakończona](./media/hana-installation/image28_install_finished.PNG)

Wystąpienie SAP HANA powinno być teraz uruchomione i gotowe do użycia. Powinno być możliwe połączenie z nim z SAP HANA Studio. Upewnij się również, że sprawdzasz i stosujesz najnowsze aktualizacje.


## <a name="next-steps"></a>Następne kroki

- [Sap HANA Duże wystąpienia wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md)

