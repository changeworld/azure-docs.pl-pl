---
title: Weryfikowanie aktualizacje oprogramowania firmy Microsoft w usłudze Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak weryfikować aktualizacji oprogramowania firmy Microsoft w ramach sprawdzania poprawności jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a9e1bb2f134e0e51803266f5e7f94c05ab6966f2
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235512"
---
# <a name="validate-software-updates-from-microsoft"></a>Weryfikowanie aktualizacje oprogramowania firmy Microsoft

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Firma Microsoft udostępni okresowe aktualizacje oprogramowania Azure Stack. Te aktualizacje są dostarczane do usługi Azure Stack wspólnej inżynierii partnerów ewentualnej udostępniane publicznie tak, aby mogli zweryfikować aktualizacje przed ich rozwiązania i przesyłanie opinii do firmy Microsoft.

## <a name="test-an-existing-solution"></a>Testowanie istniejącego rozwiązania

1. Zaloguj się do [portalu weryfikacji](https://azurestackvalidation.com).

2. Wybierz istniejące rozwiązanie gdzie zaktualizowane od firmy Microsoft został pomyślnie wdrożony i wybierz **Start** na **sprawdzanie poprawności pakietu** kafelka.

    ![Sprawdzanie poprawności pakietu](media/image3.png)

3. Wprowadź nazwę sprawdzania poprawności.

4. Wprowadź adres URL do pakietu OEM, który został zainstalowany na rozwiązania w czasie wdrażania. Użyj adresu URL dla pakietu, przechowywane w usłudze obiektów blob platformy Azure. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure storage blob do przechowywania dzienników](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

5. Wybierz **przekazywanie** można dodać pliku konfiguracji wdrożenia. Zapoznaj się [sprawdzanie poprawności nowego rozwiązania usługi Azure Stack](azure-stack-vaas-validate-solution-new.md) instrukcje dotyczące przekazywania pliku konfiguracji wdrożenia.

6. Plik konfiguracji wdrożenia muszą być następnie dostosowane przy użyciu pliku parametrów odpowiednie środowisko, zobacz [parametrów środowiska](azure-stack-vaas-parameters.md#environment-parameters) dodatkowe szczegóły.

    > [!Note]   
    > Plik konfiguracji wdrożenia można dodatkowo dostosowywać przez dodawanie wspólnych parametrów testu. Aby uzyskać więcej informacji, zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md)

7. Nazwa użytkownika i hasło dla użytkownika dzierżawy, administrator usługi i administrator chmury musi być wprowadzane ręcznie.

8. Podaj adres URL do obiektu blob usługi Azure Storage do przechowywania dzienników diagnostycznych. Aby uzyskać więcej informacji, zobacz [Tworzenie usługi Azure storage blob do przechowywania dzienników](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    > [!Note]  
    > Opisowe znaczniki mogą być wprowadzane do etykiety przepływu pracy.

10. Wybierz **przesyłania** Aby zapisać przepływ pracy.

Przepływ pracy w rozwiązaniu jest uruchamiany przez około 24 godzin. Dodaj łącze do lub instrukcji na temat planowania testów. Usuń zaznaczenie w narzędziu.

Znajdź więcej informacji na temat monitorowania postępu weryfikacji uruchamiania, zobacz [monitorowanie testu ](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
