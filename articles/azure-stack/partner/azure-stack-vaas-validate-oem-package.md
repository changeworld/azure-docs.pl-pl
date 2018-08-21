---
title: Sprawdzanie poprawności pakietów producenta sprzętu (OEM) w usłudze Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić pakiety producenta sprzętu (OEM), ze sprawdzaniem poprawności jako usługa.
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
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235480"
---
# <a name="validate-oem-packages"></a>Sprawdzanie poprawności pakietów producenta OEM

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Można przetestować nowy pakiet OEM, gdy nastąpiła zmiana oprogramowania układowego lub sterowników do weryfikacji ukończone rozwiązanie. Twój pakiet przeszedł testu, jest podpisany przez firmę Microsoft. Test musi zawierać zaktualizowany pakiet rozszerzenia producenta OEM z sterowniki i oprogramowanie układowe, które przeszły logo systemu Windows Server i komputerów z systemem testy.

Wszystkie testy zakończone w ciągu 24 godzin z wynikiem **zakończyło się pomyślnie**. Jeśli którykolwiek z testów w wyniku **nie powiodło się**, Zgłoś usterkę w [Collaborate Microsoft](https://aka.ms/collaborate) i powiadomić firmę Microsoft, wysyłając wiadomość e-mail na adres [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Pobierz pakiet OEM podpisany

1. Upewnij się, czy bieżący comiesięcznej aktualizacji zostały zastosowane. Najnowszą wersję na ten temat można znaleźć w najnowszej wersji w [dokumentacja operatora usługi Azure Stack > Przegląd > informacje o wersji](https://docs.microsoft.com/azure/azure-stack/) .

    Aktualizacji oprogramowania firmy Microsoft do usługi Azure Stack zostały oznaczone za pomocą konwencji nazewnictwa, na przykład 1803 wskazujący aktualizacji dla marca 2018 r. Dla informacji na temat zasad aktualizacji usługi Azure Stack, tempo i wersji są dostępne, zobacz [obsługi zasad w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Sprawdź stan kondycji systemu, uruchamiając **AzureStack testu** jak opisany w [przebiegu testu sprawdzania poprawności dla usługi Azure Stack. Rozwiąż wszystkie ostrzeżenia i błędy przed uruchomieniem wszystkie testy.

2. W [portalu weryfikacji](https://azurestackvalidation.com), wybierz istniejące rozwiązanie. Jeśli nie dodano rozwiązania, zobacz [dodawania nowego rozwiązania](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Wybierz **Start** na **walidacji pakietu** Kafelek, aby uruchomić nowy przepływ pracy.

    ![Sprawdzanie poprawności pakietu](media/image3.png)

4.  Udostępnia diagnostyki parametry połączenia. Aby uzyskać instrukcje, zobacz [skonfigurować konto magazynu](azure-stack-vaas-set-up-account.md).

    Pakiet rozszerzenia OEM musi być określona dla każdego przebiegu weryfikacji pakietu. Określ pakiet OEM, który został zainstalowany na rozwiązania w czasie wdrażania usługi Azure Stack. Aby uzyskać instrukcje, zobacz [Tworzenie usługi Azure storage blob do przechowywania dzienników](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    Plik w formacie JSON za pomocą zmiennych środowiskowych musi służyć do zakończenia dane wejściowe dla pola wymagane do uruchomienia uniknąć pomyłek w wprowadzania danych. Aby uzyskać instrukcje, zobacz [Pobierz plik konfiguracji we wdrożeniu usługi Azure Stack](azure-stack-vaas-parameters.md).

5. Uruchom testy.

6. Wyślij nazwę swoje rozwiązanie i pakietu sprawdzania poprawności, gdy wszystkie testy zostały pomyślnie ukończone, [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) na żądanie, podpisywanie pakietów.

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
