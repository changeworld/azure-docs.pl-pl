---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66114564"
---
Zmiany wprowadzone do udziału plików platformy Azure przy użyciu witryny Azure portal lub SMB nie od razu są wykryte i zreplikowane, takie jak zmiany punktu końcowego serwera. Usługa pliki systemu Azure nie ma jeszcze powiadomienia o zmianie lub rejestrowanie, więc nie ma możliwości automatycznie zainicjować sesję synchronizacji, gdy pliki są zmieniane. W systemie Windows Server używa usługi Azure File Sync [rejestrowanie numerów USN Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) automatycznie zainicjować sesję synchronizacji, gdy zmiany plików.<br /><br /> Aby wykryć zmiany do udziału plików platformy Azure, usługi Azure File Sync jest zaplanowane zadanie o nazwie *Zmień zadanie wykrywania*. Zadanie wykrywania zmian wylicza każdy plik w udziale plików i porównuje go do wersji synchronizacji dla tego pliku. Gdy zadanie wykrywania zmian okaże się, że pliki zostały zmienione, usługi Azure File Sync inicjuje sesję synchronizacji. Zadanie wykrywania zmian jest inicjowane co 24 godziny. Ponieważ zadanie wykrywania zmian działa wyliczając każdego pliku w udziale plików platformy Azure, wykrywania zmian trwa dłużej, w przestrzeniach nazw większych niż w mniejszej przestrzeni nazw. Dla dużych przestrzeni nazw może trwać dłużej niż co 24 godziny do określenia, które pliki uległy zmianie.<br /><br />
Uwaga: zmiany wprowadzone do udziału plików platformy Azure przy użyciu interfejsu REST nie nie aktualizacja SMB czas ostatniej modyfikacji i nie będą widoczne jako zmiany przez sync. <br /><br />
Firma Microsoft analizowania Dodawanie wykrywania zmian dla udziału plików platformy Azure, które jest podobny do numeru USN dla woluminów w systemie Windows Server. Pomóż nam określić priorytety tej funkcji do przyszłego rozwoju za oddanie głosu na na stronie [UserVoice plików Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
