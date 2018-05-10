---
title: Raport weryfikacji stosu Azure | Dokumentacja firmy Microsoft
description: Raport sprawdzania gotowości stosu Azure, aby przejrzeć wyniki sprawdzania poprawności.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a0ca0ae3ed615f6bc2774364f7a443023b911b5d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-stack-validation-report"></a>Raport weryfikacji stosu Azure
Narzędzie sprawdzania gotowości stosu Azure jest sprawdzanie poprawności, które obsługuje wdrożenia i obsługę środowiska Azure stosu. Narzędzie zapisuje wyniki sprawdzania poprawności pliku raportu JSON. Raport przedstawia szczegółowe i podsumowują dane na temat wymagań wstępnych dotyczących wdrażania stosu Azure i obrotu kluczy tajnych istniejące wdrożenia stosu Azure.  

 ## <a name="where-to-find-the-report"></a>Gdzie można znaleźć raportu
Po uruchomieniu narzędzia rejestruje ono wyniki w **AzsReadinessCheckerReport.json**. Narzędzie tworzy również dziennik o nazwie **AzsReadinessChecker.log**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell.

![Uruchom weryfikacji](./media/azure-stack-validation-report/validation.png)

Oba pliki utrwalić wyniki sprawdzanie poprawności kolejne uruchomienia na tym samym komputerze.  Na przykład narzędzie można uruchomić sprawdzania poprawności certyfikatów, uruchom ponownie, aby zweryfikować tożsamość platformy Azure i następnie raz trzeci, aby zweryfikować rejestrację. Wyniki wszystkich trzech weryfikacji są dostępne w raporcie wynikowe JSON.  

Domyślnie oba pliki są zapisywane w *C:\Users\<nazwa użytkownika > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
- Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji raportów.   
- Użyj **- CleanReport** parametru po zakończeniu wykonywania polecenia, aby wyczyścić informacje z *AzsReadinessCheckerReport.json*. o poprzednim uruchamia narzędzia.

## <a name="view-the-report"></a>Wyświetl raport
Aby wyświetlić raport w programie PowerShell, należy podać ścieżkę do raportu jako wartość **- ReportPath**. To polecenie wyświetla zawartość raportu i identyfikuje również operacji sprawdzania poprawności, które nie mają jeszcze wyników.

Na przykład aby wyświetlić raport z wiersza programu PowerShell, która jest otwarta do lokalizacji, w którym znajduje się raport, uruchom polecenie: 
   > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json` 

Dane wyjściowe podobne poniższej ilustracji:

![Wyświetl raport](./media/azure-stack-validation-report/view-report.png)

## <a name="view-the-report-summary"></a>Wyświetl raport podsumowania
Aby wyświetlić podsumowanie raportu, można dodać **— Podsumowanie** przełączyć się na końcu wiersza polecenia programu PowerShell. Na przykład: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -summary`  

Podsumowanie zawiera operacji sprawdzania poprawności, które nie mają wyników i wskazuje zakończone powodzeniem lub niepowodzeniem dla operacji sprawdzania poprawności, które są spełnione. Dane wyjściowe podobne poniższej ilustracji:

![Raport Podsumowanie](./media/azure-stack-validation-report/report-summary.png)


## <a name="view-a-filtered-report"></a>Wyświetl raport filtrowane
Aby wyświetlić raport przefiltrowane na jednym typie weryfikacji, należy użyć **- ReportSections** parametru i określić jedną z następujących wartości, które odpowiada typowi weryfikacji, który chcesz wyświetlić:
- Certyfikat
- AzureRegistration
- AzureIdentity
- Zadania   
- Wszyscy  

Na przykład aby wyświetlić raport Podsumowanie dla certyfikatów tylko, użyj następującego polecenia programu PowerShell: 
 > `Start-AzsReadinessChecker -ReportPath .\AzsReadinessReport.json -ReportSections Certificate – Summary`


## <a name="see-also"></a>Zobacz także
[Dokumentacja poleceń cmdlet Start-AzsReadinessChecker](azure-stack-azsreadiness-cmdlet.md)
