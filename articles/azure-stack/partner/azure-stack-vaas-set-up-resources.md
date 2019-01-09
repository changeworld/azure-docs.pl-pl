---
title: Samouczek — Konfigurowanie zasobów do weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się, jak skonfigurować zasoby do weryfikacji jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 58c69840e7b8ebdc2bd367859184ab54460c9426
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103482"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Samouczek: Konfigurowanie zasobów do weryfikacji jako usługa

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Należy utworzyć rozwiązanie. Weryfikacja jako rozwiązanie usługi (VaaS) reprezentuje rozwiązanie usługi Azure Stack z BOM konkretnego sprzętu. Rozwiązanie użyje do sprawdzenia, czy dany sprzęt mogą obsługiwać wykonywania usługi Azure Stack. Wykonaj kroki tego samouczka, aby przygotować się do korzystania z usługi za pomocą rozwiązania.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przygotowywanie do konfigurowania usługi Azure AD za pomocą VaaS wystąpienia (Azure AD).
> * Tworzenie konta magazynu

## <a name="configure-an-azure-ad-tenant"></a>Konfigurowanie dzierżawę usługi Azure AD

Dzierżawa usługi Azure AD jest wymagana dla uwierzytelniania i rejestracji za pomocą VaaS. Funkcje kontroli dostępu opartej na rolach dzierżawy będzie służyć przez partnera do zarządzania, które osoby w organizacji partnerskiej służy VaaS.

Rejestrowanie organizacji usługi Azure AD dzierżawy katalogu (zamiast katalogu dzierżawy usługi Azure AD, które są używane dla usługi Azure Stack) i ustanowienia zasad do zarządzania kontami użytkowników w nim. Aby uzyskać więcej informacji, zobacz [Zarządzanie swoim katalogiem usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-administer).

### <a name="create-a-tenant"></a>Tworzenie dzierżawy

Utworzyć dzierżawę specjalnie do użycia przy VaaS nazwą opisową, na przykład `ContosoVaaS@onmicrosoft.com`.

1. Tworzenie dzierżawy usługi Azure AD w [witryny Azure portal](https://portal.azure.com), lub użyj istniejącej dzierżawy. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Dodaj członków organizacji dzierżawcy. Tych użytkowników będzie odpowiedzialny za pomocą usługi, aby wyświetlić lub zaplanować testy. Po zakończeniu rejestracji zostanie zdefiniować poziomy dostępu użytkowników.
 
    Autoryzowanie użytkowników w dzierżawie, do uruchamiania działań w VaaS przez przypisanie jednego z następujących ról:

    | Nazwa roli | Opis |
    |---------------------|------------------------------------------|
    | Właściciel | Ma pełny dostęp do wszystkich zasobów. |
    | Czytelnik | Wyświetlanie wszystkich zasobów, ale nie może utworzyć ani zarządzać. |
    | Współautor testu | Można tworzyć i zarządzać zasobami testu. |

    Aby przypisać role w **usługi Azure Stack weryfikacji Service** aplikacji:

    1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
    2. Wybierz **wszystkich usług** > **usługi Azure Active Directory** w obszarze **tożsamości** sekcji.
    3. Wybierz **aplikacje dla przedsiębiorstw** > **usługi Azure Stack weryfikacji Service** aplikacji.
    4. Wybierz pozycję **Użytkownicy i grupy**. **Azure Stack weryfikacji Service — użytkownicy i grupy** blok zawiera listę użytkowników z uprawnieniami do korzystania z aplikacji.
    5. Wybierz **+ Dodaj użytkownika** Dodawanie użytkownika z dzierżawy i przypisać rolę.
   
    Jeśli chcesz izolować VaaS zasobów i akcji wśród różnych grup w organizacji, możesz utworzyć wiele katalogów dzierżawy usługi Azure AD.

### <a name="register-your-tenant"></a>Zarejestrować dzierżawę usługi

Ten proces zezwala dzierżawy za pomocą **usługi Azure Stack weryfikacji Service** aplikacji usługi Azure AD.

1. Wyślij następujące informacje na temat dzierżawy do firmy Microsoft w [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

    | Dane | Opis |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Nazwa organizacji | Nazwa organizacji oficjalnych. |
    | Nazwa katalogu dzierżawy usługi Azure AD | Nazwa katalogu dzierżawy usługi Azure AD, jest zarejestrowany. |
    | Identyfikator katalogu dzierżawy usługi Azure AD | Katalog dzierżawy usługi Azure AD identyfikator GUID skojarzony z katalogiem. Aby uzyskać informacje na temat można znaleźć swój identyfikator katalogu dzierżawy usługi Azure AD, zobacz [uzyskanie Identyfikatora dzierżawy](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id). |

2. Czekaj na potwierdzenie od zespołu usługi Azure Stack weryfikacji, aby sprawdzić, czy w dzierżawie mogą korzystać z portalu VaaS.

### <a name="consent-to-the-vaas-application"></a>Zgoda na aplikację VaaS

Jako administrator usługi Azure AD należy podać aplikacji VaaS usługi Azure AD wymagane uprawnienia w imieniu swojej dzierżawy:

1. Użyj poświadczeń administratora globalnego dla dzierżawy do logowania się do [VaaS portal](https://azurestackvalidation.com/). 

2. Wybierz **Moje konto**.

3 Zaakceptuj warunki, aby kontynuować po wyświetleniu monitu o nadanie uprawnień VaaS wymienione usługi Azure AD.

## <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage

Podczas wykonywania testów VaaS danych wyjściowych dzienników diagnostycznych do konta usługi Azure Storage. Oprócz dzienników testu konto magazynu może również pakiety rozszerzeń przekazywania przez producenta OEM dla przepływu pracy weryfikacji rozwiązania.

Konto usługi Azure Storage jest hostowana w chmurze publicznej Azure, nie w środowisku usługi Azure Stack.

1. W witrynie Azure portal wybierz **wszystkich usług** > **magazynu** > **kont magazynu**. Na **kont magazynu** bloku wybierz **Dodaj**.

2. Wybierz subskrypcję, w ramach której chcesz utworzyć konto magazynu.

3. W obszarze **grupy zasobów**, wybierz opcję **Utwórz nową**. Wprowadź nazwę dla nowej grupy zasobów.

4. Wprowadź nazwę konta magazynu. Wybrana nazwa musi być:
    - Unikatowa w obrębie platformy Azure
    - Od 3 do 24 znaków
    - Zawierać tylko cyfry i małe litery

5. Wybierz **zachodnie stany USA** region dla swojego konta magazynu.

    Aby zapewnić, że sieci są naliczane opłaty za nie przechowywania dzienników, można skonfigurować konto usługi Azure Storage używana będzie tylko **zachodnie stany USA** regionu. Replikacja danych i funkcji warstwy magazynu gorącego nie są wymagane dla tych danych. Włączenie funkcji albo znacznie zwiększy koszty.

6. Pozostaw ustawienia do wartości domyślnych, z wyjątkiem **rodzaj konta**:

    - **Modelu wdrażania przy użyciu** pole jest ustawione na **usługi Resource Manager** domyślnie.
    - Pole **Wydajność** jest domyślnie ustawiane na wartość **Standardowa**.
    - Wybierz **rodzaj konta** pola jako **magazynu obiektów Blob**.
    - **Pola replikacji** ustawiono **magazyn lokalnie nadmiarowy (LRS)** domyślnie.
    - **Warstwa dostępu** jest domyślnie ustawiana na **Gorąca**.

7. Kliknij pozycję **Przejrzyj i utwórz** , aby przejrzeć ustawienia konta magazynu i utworzyć konto.

## <a name="next-steps"></a>Kolejne kroki

Jeśli środowisko nie zezwala na połączenia w granicy, należy wykonać kroki samouczka na temat wdrażania lokalnego agenta, aby uruchomić test na danym urządzeniu.

> [!div class="nextstepaction"]
> [Wdrażanie lokalnego agenta](azure-stack-vaas-local-agent.md)
