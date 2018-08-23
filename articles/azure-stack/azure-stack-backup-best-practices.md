---
title: Infrastruktura usługi Backup najlepsze rozwiązania dotyczące usługi Azure Stack | Dokumentacja firmy Microsoft
description: W przypadku wdrażania i zarządzania usługi Azure Stack w Twoim centrum danych, aby ułatwić uniknięcie utraty danych, w przypadku poważnej awarii, możesz wykonać zestaw najlepszych rozwiązań.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 08d8822410545fb0ae3a2a99de00b38566c9834c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42059524"
---
# <a name="infrastructure-backup-service-best-practices"></a>Najlepszych rozwiązań dotyczących infrastruktury usługi Backup

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

W przypadku wdrażania i zarządzania usługi Azure Stack w Twoim centrum danych, aby ułatwić uniknięcie utraty danych w przypadku poważnej awarii, możesz wykonać najlepszych rozwiązań.

Najlepsze rozwiązania należy przejrzeć w regularnych odstępach czasu, aby zweryfikować, że instalacja jest nadal w zakresie zgodności podczas wprowadzania zmian do przepływu operacji. Należy napotkasz jakiekolwiek problemy podczas implementowania tych najlepszych rozwiązań, skontaktuj się z Microsoft Support pomocy.

## <a name="configuration-best-practices"></a>Najlepszymi rozwiązaniami konfiguracyjnymi

### <a name="deployment"></a>Wdrożenie

Włącz tworzenie kopii zapasowych po wdrożeniu poszczególnych w chmurze Azure Stack. Za pomocą usługi Azure Stack PowerShell można zaplanować tworzenie kopii zapasowej z dowolnego klienta/serwera z dostępem do punktu końcowego interfejsu API zarządzania operator.

### <a name="networking"></a>Networking

Ciąg Universal Naming Convention (UNC) dla ścieżki, należy użyć w pełni kwalifikowaną nazwę domeny (FQDN). Adres IP jest możliwe w przypadku rozpoznawania nazw nie jest możliwe. Ciąg UNC Określa lokalizację zasobów, takich jak udostępnione pliki lub urządzeń.

### <a name="encryption"></a>Szyfrowanie

Klucz szyfrowania jest używany do szyfrowania danych kopii zapasowej, które są eksportowane do magazynu zewnętrznego. Klucz jest generowany jako część [opcja włączania kopii zapasowych dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

Klucz musi być przechowywany w bezpiecznym miejscu (na przykład, publiczne usługi Azure Key Vault klucz tajny). Ten klucz musi używany podczas ponownego wdrażania usługi Azure Stack. 

![Przechowywany klucz bezpiecznej lokalizacji.](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>Najlepsze rozwiązanie w zakresie

### <a name="backups"></a>Tworzenie kopii zapasowych

 - Infrastruktura kopii zapasowej kontrolera musi być wyzwalane na żądanie. Zaleca się kopia zapasowa co najmniej dwa razy dziennie.
 - Zadania tworzenia kopii zapasowej wykonać, gdy system działa, więc nie ma bez przerwy w działaniu do środowiska zarządzania lub aplikacji użytkownika. Oczekiwać, że zadania tworzenia kopii zapasowej na 20 – 40 minut dla rozwiązania, który jest uzasadnione obciążeniem.
 - Za pomocą producenta OEM, pod warunkiem instrukcji, przełączniki sieciowe ręcznego tworzenia kopii zapasowych i sprzętu hosta cyklu życia (HLH) powinny być przechowywane w tym samym udziale kopii zapasowej, gdzie dane kopii zapasowej na płaszczyźnie kontroli magazynów kopii zapasowej kontrolera infrastruktury. Rozważ przechowywanie przełącznika i konfiguracje HLH w folderze na region. Jeśli masz wiele wystąpień usługi Azure Stack, w tym samym regionie, należy wziąć pod uwagę przy użyciu identyfikatora dla każdej konfiguracji, do której należy jednostka skalowania.

### <a name="folder-names"></a>Nazwy folderów

 - Infrastruktury automatycznie tworzy MASBACKUP folder. Jest to udział zarządzanych przez firmę Microsoft. Na tym samym poziomie co MASBACKUP, można utworzyć udziały. Nie zaleca się tworzenie folderów lub magazynu danych wewnątrz MASBACKUP, który nie powoduje utworzenia usługi Azure Stack. 
 -  Użytkownik pełni kwalifikowaną nazwę domeny i regionie, nazwę folderu do odróżnienia kopii zapasowych danych z różnych chmur. W pełni kwalifikowana nazwa domeny (FQDN) punktów końcowych czy wdrożenia usługi Azure Stack jest kombinacją Region i parametru zewnętrznej nazwy domeny. Aby uzyskać więcej informacji, zobacz [Integracja z centrum danych usługi Azure Stack — DNS](azure-stack-integrate-dns.md).

Na przykład udziału kopii zapasowej jest AzSBackups w serwisie fileserver01.contoso.com. W tym udziale plików może być folderem na wdrożenie usługi Azure Stack przy użyciu nazwy domeny zewnętrznej i podfolder, który używa nazwy regionu. 

Nazwa FQDN: contoso.com  
Region: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup folder jest o tym, gdzie usługi Azure Stack przechowuje dane kopii zapasowej. Nie należy używać tego folderu do przechowywania swoich danych. Producent OEM nie należy używać tego folderu do przechowywania danych kopii zapasowej albo. 

Producenci OEM są zachęcani do przechowywania danych kopii zapasowej dla ich elementy w folderze regionu. Każdy przełączniki sieciowe, sprzęt cyklu życia hosta (HLH) i tak dalej, mogą być przechowywane w podfolderze swój własny. Na przykład:

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>Monitorowanie

System obsługuje następujące alerty:

| Alerty                                                   | Opis                                                                                     | Korygowanie                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Niepowodzenie wykonywania kopii zapasowej, ponieważ udział plików jest poza pojemności | Udział plików jest poza pojemności i tworzenia kopii zapasowej kontrolera nie można wyeksportować pliki kopii zapasowej do lokalizacji. | Dodaj więcej pojemności magazynu i ponownie utwórz kopię zapasową. Usuń istniejące kopie zapasowe (począwszy od najstarszych najpierw), aby zwolnić miejsce.                    |
| Niepowodzenie wykonywania kopii zapasowej z powodu problemów z łącznością.             | Sieci od usługi Azure Stack i pliku udziału ma problemy.                          | Rozwiązać problem z siecią, a następnie ponów kopii zapasowej.                                                                                            |
| Niepowodzenie wykonywania kopii zapasowej z powodu błędów w ścieżce                | Nie można rozpoznać ścieżki udziału plików                                                          | Mapowanie udziału za pomocą innego komputera, aby upewnić się, że udział jest dostępny. Może być konieczne należy zaktualizować ścieżkę, jeśli nie jest już prawidłowy.       |
| Niepowodzenie wykonywania kopii zapasowej z powodu błędu uwierzytelniania               | Może to być problem z poświadczeniami lub problem z siecią, która ma wpływ na uwierzytelnianie.    | Mapowanie udziału za pomocą innego komputera, aby upewnić się, że udział jest dostępny. Może być konieczne zaktualizowanie poświadczeń w przypadku, jeśli one nie są już prawidłowe. |
| Niepowodzenie wykonywania kopii zapasowej z powodu ogólnego błędu                    | Żądania zakończone niepowodzeniem może wynikać z wystąpienia sporadycznie problemu. Spróbuj ponownie wykonać kopię zapasową.                    | Zadzwoń do pomocy technicznej                                                                                                                               |

## <a name="next-steps"></a>Kolejne kroki

 - Przejrzyj materiał referencyjny dla [infrastruktura kopii zapasowej usługa](azure-stack-backup-reference.md).  
 - Włącz [infrastruktura kopii zapasowej usługa](azure-stack-backup-enable-backup-console.md).
