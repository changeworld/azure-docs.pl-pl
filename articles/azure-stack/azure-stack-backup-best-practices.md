---
title: Infrastruktura usługi Kopia zapasowa najlepsze rozwiązania dotyczące stosu Azure | Dokumentacja firmy Microsoft
description: Można wykonać zestawu najlepszych rozwiązań, wdrażania i zarządzania Azure stosu w centrum danych, aby ułatwić uniknięcie utraty danych w przypadku poważnej awarii.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: ec30832e6863ad92eff8f5c2e613adc503c73af5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075752"
---
# <a name="infrastructure-backup-service-best-practices"></a>Infrastruktura usługi Kopia zapasowa najlepsze rozwiązania

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można stosować najlepsze rozwiązania, wdrażania i zarządzania Azure stosu w centrum danych, aby ułatwić uniknięcie utraty danych w przypadku poważnej awarii.

Należy przejrzeć najlepsze rozwiązania w regularnych odstępach czasu, aby sprawdzić, czy instalacja jest nadal w zgodności podczas wprowadzania zmian do przepływu operacji. Należy wystąpią problemy podczas implementowania następujące najlepsze rozwiązania, skontaktuj się z Microsoft Support pomocy.

## <a name="configuration-best-practices"></a>Najlepsze rozwiązania w zakresie konfiguracji

### <a name="deployment"></a>Wdrożenie

Włączenia kopii zapasowej infrastruktury po wdrożeniu każdej chmury stosu Azure. Przy użyciu narzędzia AzureStack, można zaplanować tworzenie kopii zapasowych z dowolnego klienta/serwera dostępu do punktu końcowego interfejsu API zarządzania operatora.

### <a name="networking"></a>Networking

Ciąg konwencji UNC (Universal Naming) dla ścieżki należy użyć w pełni kwalifikowaną nazwą domeny (FQDN). Adres IP jest możliwe, gdy rozpoznawania nazw nie jest możliwe. Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń.

### <a name="encryption"></a>Szyfrowanie

Klucz szyfrowania jest używany do szyfrowania danych kopii zapasowej, który pobiera wyeksportowany do magazynu zewnętrznego. Klucz jest generowany przy użyciu narzędzia AzureStack. 

![Narzędzia AzureStack](media\azure-stack-backup\azure-stack-backup-encryption1.png)

Klucz musi być przechowywany w bezpiecznym miejscu (na przykład publicznej usługi Azure Key Vault klucz tajny). Ten klucz, należy użyć podczas ponownego wdrażania stosu Azure. 

![Przechowywany klucz bezpiecznej lokalizacji.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Najlepsze rozwiązanie w zakresie

### <a name="backups"></a>Tworzenie kopii zapasowych

 - Infrastruktura kopii zapasowej kontrolera, należy uruchomić na żądanie. Zaleca się tworzenia kopii zapasowej co najmniej dwa razy dziennie.
 - Zadania tworzenia kopii zapasowej wykonać, gdy system jest uruchomiony, więc nie ma żadnych przestojów do środowiska zarządzania lub aplikacji użytkownika. Oczekiwać, że zadania tworzenia kopii zapasowej, na 20 40 minut dla rozwiązania, który jest obciążony uzasadnione.
 - Za pomocą instrukcji podane przez producenta OEM, przełączniki sieciowe ręcznie kopii zapasowej i sprzętu hosta cyklu życia (HLH) powinny być przechowywane na tym samym udziale kopii zapasowej gdzie kontroli magazynów kopii zapasowej kontrolera infrastruktury płaszczyzny dane kopii zapasowej. Warto przechowywać przełącznika i konfiguracje HLH w folderze regionu. Jeśli masz wiele wystąpień stosu Azure w tym samym regionie, należy wziąć pod uwagę przy użyciu identyfikatora dla każdej konfiguracji, który należy do jednostki skalowania.

### <a name="folder-names"></a>Nazwy folderów

 - Infrastruktura automatycznie tworzy MASBACKUP folder. To jest udziałem zarządzany przez firmę Microsoft. Na tym samym poziomie jako MASBACKUP mogą tworzyć udziały. Trwa tworzenie folderów lub magazynu danych wewnątrz MASBACKUP, która nie tworzy stosu Azure nie jest zalecane. 
 -  Użytkownik nazwy FQDN i regionu w nazwie folderu do odróżnienia kopii zapasowych danych z różnych chmur. W pełni kwalifikowaną nazwę (FQDN) wdrożenia stosu Azure i punkty końcowe jest kombinacją parametr regionu i parametr zewnętrzną nazwę domeny. Aby uzyskać więcej informacji, zobacz [integracja centrum danych Azure stosu - DNS](azure-stack-integrate-dns.md).

Na przykład udziale kopii zapasowej jest hostowany na fileserver01.contoso.com AzSBackups. W tego udziału pliku może być folderem na wdrożenie stosu Azure przy użyciu nazwy domen zewnętrznych i podfolder, który używa nazwy obszaru. 

Nazwa FQDN: contoso.com  
Region: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup folder jest, gdzie stosu Azure przechowuje dane kopii zapasowej. Nie należy używać tego folderu do przechowywania danych użytkownika. Przez producenta OEM nie powinni używać tego folderu do przechowywania danych kopii zapasowej albo. 

OEM zachęca się do przechowywania danych kopii zapasowych dla swoich składników w folderze regionu. Każdy przełączniki sieciowe, sprzęt hosta cyklu życia (HLH) i tak dalej mogą być przechowywane we własnym podfolderze. Na przykład:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitorowanie

Następujące alerty są obsługiwane przez system:

| Alerty                                                   | Opis                                                                                     | Korygowanie                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Niepowodzenie wykonywania kopii zapasowej, ponieważ udział plików jest poza pojemność | Udział plików jest poza pojemność i tworzenia kopii zapasowej kontrolera nie można wyeksportować pliki kopii zapasowej do lokalizacji. | Dodawanie większej pojemności magazynu i spróbuj ponownie wykonać kopię zapasową. Usuń istniejące kopie zapasowe (począwszy od najstarszych najpierw), aby zwolnić miejsce.                    |
| Kopii zapasowej nie powiodło się z powodu problemów z łącznością.             | Sieci między stosu Azure a plikiem udziału wystąpiły problemy.                          | Rozwiąż problem z siecią i spróbuj ponownie kopii zapasowej.                                                                                            |
| Kopii zapasowej nie powiodło się z powodu błędów w ścieżce                | Nie można rozpoznać ścieżki udziału plików                                                          | Mapowanie udziału za pomocą innego komputera, aby upewnić się, że udział jest dostępny. Należy zaktualizować ścieżkę, jeśli nie jest już prawidłowy.       |
| Kopii zapasowej nie powiodło się z powodu problemu uwierzytelniania               | Może to być problem z poświadczeniami lub problem z siecią, który ma wpływ na uwierzytelniania.    | Mapowanie udziału za pomocą innego komputera, aby upewnić się, że udział jest dostępny. Może być konieczne zaktualizowanie poświadczeń, jeśli nie są już prawidłowe. |
| Kopii zapasowej nie powiodło się z powodu ogólnego błędu                    | Żądań zakończonych niepowodzeniem może wynikać z wystąpił tymczasowy problem. Spróbuj ponownie wykonać kopię zapasową.                    | Zadzwoń do pomocy technicznej                                                                                                                               |

## <a name="next-steps"></a>Kolejne kroki

 - Przejrzyj materiału odwołania do [infrastruktury usługi Kopia zapasowa](azure-stack-backup-reference.md).  
 - Włącz [infrastruktury usługi tworzenia kopii zapasowej](azure-stack-backup-enable-backup-console.md).