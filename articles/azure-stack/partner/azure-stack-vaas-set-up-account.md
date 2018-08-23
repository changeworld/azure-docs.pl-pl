---
title: Konfigurowanie walidacji jako konto usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować weryfikację jako konto usługi.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 3f87721dcf7485e15a473e82597f6bf4baeca659
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "42139428"
---
# <a name="set-up-your-validation-as-a-service-account"></a>Konfigurowanie walidacji jako konto usługi

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Weryfikacja jako usługa (VaaS) jest usługą platformy Azure, która ma zostać udostępnione dla partnerów Microsoft Azure Stack, posiadający umowę wspólnej engineering w firmie Microsoft w celu projektowania, opracowywania, sprawdzanie poprawności, sprzedaży, wdrażania i obsługi rozwiązań usługi Azure Stack na rynku.

Dowiedz się, jak się przygotować system do weryfikacji jako usługa. Skonfiguruj wystąpienie usługi Azure Active Directory i wykonywać inne zadania wymagane w celu uzyskania gotowych do użycia VaaS. 

Zadania obejmują:

- Tworzenie obiektu blob usługi Azure Storage do przechowywania dzienników
- Wdrażanie lokalnego agenta
- Pobierz testowych obrazów maszyn wirtualnych w wystąpieniu usługi Azure Stack, które ma zostać przetestowana

## <a name="create-an-azure-active-directory-tenant-id"></a>Tworzenie Identyfikatora dzierżawy usługi Azure Active Directory

1. Tworzenie dzierżawy usługi Azure Active Directory w [witryny Azure portal](https://portal.azure.com) lub użyć istniejącej dzierżawy.

    Zaleca się utworzyć dzierżawę specjalnie do użycia przy VaaS nazwą opisową, takich jak ContosoVaaS@onmicrosoft.com. Funkcje kontroli dostępu opartej na rolach dzierżawy będzie służyć przez partnera do zarządzania, które osoby w organizacji partnerskiej służy VaaS.  
    
    Więcej informacji, zobacz [Zarządzanie swoim katalogiem usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

    > [!Note]  
    > Aby uzyskać więcej informacji na temat tworzenia nowych dzierżaw usługi Azure Active Directory, zobacz [Rozpoczynanie pracy z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad).

2. Dodaj członków organizacji, którzy będzie odpowiedzialna za korzystanie z usługi do dzierżawy. Przypisz każdego użytkownika w dzierżawie, jedną z następujących ról do ich kontrolę dostępu do VaaS:

    | Nazwa roli | Opis |
    |---------------------|------------------------------------------|
    | Właściciel | Ma pełny dostęp do wszystkich zasobów. |
    | Czytelnik | Może wyświetlać wszystkie zasoby, ale nie edytować. |
    | Współautor testu | Umożliwia zarządzanie zasobami testu. |
    | Współautor katalogu | Umożliwia zarządzanie zasobami publikowania rozwiązania. |

## <a name="set-up-your-tenant"></a>Konfigurowanie Twojej dzierżawy

Konfigurowanie Twojej dzierżawy w **usługi Azure Stack weryfikacji Service** aplikacji. 

1. Wyślij następujące informacje na temat dzierżawy do firmy Microsoft w vaashelp@microsoft.com.

    | Dane | Opis |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nazwa organizacji | Nazwa organizacji oficjalnych. |
    | Nazwa katalogu dzierżawy usługi Azure AD | Nazwa katalogu dzierżawy usługi AD Azure jest zarejestrowany. |
    | Identyfikator katalogu dzierżawy usługi Azure AD | Usługa Azure Directory dzierżawy usługi AD identyfikator GUID skojarzony z katalogiem.<br> Aby uzyskać informacje na temat można znaleźć swój identyfikator katalogu dzierżawy usługi Azure AD można znaleźć, zobacz "[uzyskanie Identyfikatora dzierżawy](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)." |

    

2. Zespół usługi Azure Stack oferuje potwierdzenie, że dzierżawy mogą używać portalu VaaS.

3. Użyj poświadczeń administratora globalnego dla dzierżawy do logowania się do [VaaS portal](https://azurestackvalidation.com/
). Wybierz **Moje konto**.

    ![Zaloguj się do portalu VaaS](media/vaas_portalsignin.png)

3. Witryna monitowany do udzielania dostępu dla VaaS. Zaakceptuj warunki, aby kontynuować.

## <a name="assign-user-roles"></a>Przypisz role użytkownika

Aby przypisać rolę użytkownika:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz **wszystkich usług** > **usługi Azure Active Directory** w **tożsamości** grupy.
3. Wybierz **aplikacje dla przedsiębiorstw** > **usługi Azure Stack weryfikacji Service** aplikacji.
4. Wybierz pozycję **Użytkownicy i grupy**. **Azure Stack weryfikacji Service — użytkownicy i grupy** blok zawiera listę użytkowników z uprawnieniami do korzystania z aplikacji.
5. Wybierz **+ Dodaj użytkownika** można dodać przypisania.

## <a name="create-an-azure-storage-blob-to-store-logs"></a>Tworzenie usługi Azure storage blob do przechowywania dzienników

VaaS tworzy dzienniki diagnostyczne podczas wykonywania testów sprawdzania poprawności. Potrzebna jest lokalizacja usługi obiektów blob platformy Azure URL sygnatury dostępu Współdzielonego do przechowywania dzienników. Konto magazynu może również służyć do pakietów dostosowywania magazynu przez producenta OEM.

Te kroki przedstawiają sposób konfigurowania i wygenerować identyfikator URI magazynu as-a-service (SAS) dla konta usługi Azure storage i gdzie można określić konto magazynu w portalu VaaS podczas uruchamiania testów w portalu.

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

1. Aby utworzyć konto magazynu, postępuj zgodnie z instrukcjami [Tworzenie konta magazynu](../../storage/common/storage-quickstart-create-account.md).

2. Po wybraniu typu konta magazynu, wybierz **magazynu obiektów Blob** typ konta.

### <a name="generate-a-sas-url-for-the-storage-account"></a>Generuj adres URL sygnatury dostępu Współdzielonego dla konta magazynu

1. Przejdź do konta magazynu utworzonego powyżej.

2. W bloku, w obszarze **ustawienia**, wybierz opcję **sygnatury dostępu współdzielonego**.

3. Tylko sprawdzanie **Blob** z **opcji dozwolone usług** (Usuń zaznaczenie pola wyboru pozostałych).

4. Sprawdź **usługi**, ** kontenera i **obiektu** z **dozwolone typy zasobów**.

5. Sprawdź **odczytu**, **zapisu**, **listy**, **Dodaj**, **Utwórz** z **dozwolone uprawnienia**  (Usuń zaznaczenie pola wyboru pozostałych).

6. Ustaw **czas rozpoczęcia** do bieżącego czasu i **czas zakończenia** na trzy miesiące od bieżącego czasu.

7. Wybierz **Generowanie sygnatury dostępu Współdzielonego i parametry połączenia** i Zapisz **adres URL SAS usługi Blob** ciągu.

> [!Note]  
> Adres URL sygnatury dostępu Współdzielonego wygasa w momencie zakończenia ustawić podczas generowania adresu URL. Upewnij się, że adres URL jest wystarczająco prawidłowy przed ich udostępnieniem z zespołem produktu na potrzeby debugowania lub adres URL jest prawidłowy dla więcej niż 30 dni, podczas planowania testów.

## <a name="next-steps"></a>Kolejne kroki

- Agent lokalny VaaS umożliwia sprawdzanie sprzętu. Aby uzyskać instrukcje, zobacz [wdrażanie lokalnych maszyn wirtualnych agenta i testowania](azure-stack-vaas-test-vm.md).
- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).