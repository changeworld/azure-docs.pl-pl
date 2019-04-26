---
title: Azure AD Connect — aktualizacja certyfikatu SSL dla farmy usług AD FS | Dokumentacja firmy Microsoft
description: W tym dokumencie szczegółowe kroki, aby zaktualizować certyfikat SSL farmy usług AD FS przy użyciu usługi Azure AD Connect.
services: active-directory
manager: mtillman
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory  
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/09/2018
ms.date: 11/09/2018
ms.component: hybrid
author: billmath
ms.custom: seohack1
ms.author: v-junlch
ms.openlocfilehash: 39ac0e9cf11a0c6c212c4beadb6635ad2b6b056d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60244681"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizacja certyfikatu SSL dla farmy usługi Active Directory Federation Services (AD FS)

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak program Azure AD Connect umożliwia zaktualizowanie certyfikatu SSL dla farmy usługi Active Directory Federation Services (AD FS). Narzędzie Azure AD Connect można łatwo zaktualizować certyfikatu SSL dla farmy usług AD FS, nawet jeśli użytkownika logowania wybrano metodę nie usług AD FS.

Możesz wykonać całą operację aktualizacji certyfikatu SSL dla farmy usług AD FS we wszystkich federacyjnych i serwerach Proxy aplikacji sieci Web (WAP) w trzech prostych krokach:

![Trzy kroki](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Aby dowiedzieć się więcej na temat certyfikatów, które są używane przez usługi AD FS, zobacz [Opis certyfikatów używane przez usługi AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Wymagania wstępne

- **Farma usług AD FS**: Upewnij się, że kolektyw serwerów usług AD FS systemu Windows Server 2012 R2 lub nowszym.
- **Azure AD Connect**: Upewnij się, że wersja programu Azure AD Connect 1.1.553.0 lub nowszej. Użyjesz zadanie **aktualizacji usługi AD FS certyfikat**.

![Aktualizacja zadania protokołu SSL](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: Podaj informacje farmy usług AD FS

Program Azure AD Connect podejmuje próbę uzyskania informacji o farmie usług AD FS automatycznie przez:
1. Wykonywanie zapytania informacji o farmie z usług AD FS (Windows Server 2016 lub nowszego).
2. Odwołania do informacji z poprzednich przebiegów, które są przechowywane lokalnie za pomocą usługi Azure AD Connect.

Można zmodyfikować listę serwerów, które są wyświetlane przez dodawanie lub usuwanie serwerów w celu odzwierciedlenia bieżącej konfiguracji farmy usług AD FS. Jak podano informacje o serwerze, program Azure AD Connect Wyświetla łączności i bieżącego stanu certyfikatu SSL.

![Informacje o serwerze programu AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Jeśli lista zawiera serwer, który nie jest już częścią farmy usług AD FS, kliknij przycisk **Usuń** można usunąć serwera z listy serwerów w farmie usług AD FS.

![Serwer w trybie offline na liście](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Usuwanie serwera z listy serwerów dla farmy usług AD FS w Azure AD Connect jest operacją lokalnych i aktualizuje informacje o dla farmy usług AD FS, zawierającą lokalnie w usłudze Azure AD Connect. Program Azure AD Connect nie modyfikuje konfiguracji w usługach AD FS w celu odzwierciedlenia zmiany.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Krok 2: Podaj nowy certyfikat SSL

Po potwierdzeniu informacji na temat serwerów do farmy usług AD FS, Azure AD Connect poprosi o podanie nowego certyfikatu SSL. Podaj certyfikat PFX chronionych hasłem w celu kontynuowania instalacji.

![Certyfikat SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Po podaniu certyfikatu usługi Azure AD Connect przechodzi przez szereg wymagań wstępnych. Sprawdź certyfikat, aby upewnić się, że certyfikat jest prawidłowy dla farmy usług AD FS:

-   Nazwa podmiotu nazwy/alternatywny podmiot certyfikatu jest taki sam jak nazwa usługi federacyjnej lub jest certyfikat wieloznaczny.
-   Certyfikat jest ważny przez dłużej niż 30 dni.
-   Łańcuch zaufania certyfikatów jest nieprawidłowy.
-   Certyfikat jest chroniony hasłem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Wybierz serwery aktualizacji

W następnym kroku wybierz serwery, które muszą mieć certyfikat SSL, aktualizowane. Nie można wybrać serwerów, które są w trybie offline dla aktualizacji.

![Wybierz serwery, aby zaktualizować](./media/how-to-connect-fed-ssl-update/selectservers.png)

Po zakończeniu konfigurowania, program Azure AD Connect wyświetla komunikat, który wskazuje stan aktualizacji i udostępnia opcję, aby sprawdzić logowania usług AD FS.

![Ukończono konfigurację](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Często zadawane pytania

- **Co powinna być nazwa podmiotu certyfikatu dla nowego certyfikatu SSL usług AD FS?**

    Program Azure AD Connect sprawdza, czy nazwa podmiotu nazwy/alternatywny podmiotu certyfikatu zawiera nazwa usługi federacyjnej. Na przykład jeśli nazwa usługi federacyjnej jest fs.contoso.com, nazwa podmiotu nazwy/alternatywny podmiotu musi być fs.contoso.com.  Symbol wieloznaczny certyfikatów również są akceptowane.

- **Dlaczego pojawia się pytanie o poświadczenia ponownie na stronie serwer proxy aplikacji sieci Web?**

    Jeśli poświadczenia, których udzielasz do łączenia się z serwerami usług AD FS nie mają również uprawnienie do zarządzania serwerami proxy aplikacji sieci Web, program Azure AD Connect poprosi o podanie poświadczeń, które mają uprawnienia administracyjne na serwerach proxy aplikacji sieci Web.

- **Serwer jest wyświetlany w trybie offline. Co zrobić?**

    Program Azure AD Connect nie może wykonać żadnych operacji, jeśli serwer jest w trybie offline. Jeśli serwer jest częścią farmy usług AD FS, sprawdź łączność z serwerem. Po rozwiązaniu problemu, naciśnij ikonę odświeżenia, aby zaktualizować stan w kreatorze. Jeśli serwer był częścią farmy wcześniej, ale teraz już nie istnieje, kliknij przycisk **Usuń** go usunąć z listy serwerów ten program Azure AD Connect obsługuje. Usuwanie serwera z listy w programie Azure AD Connect nie powoduje zmian konfiguracji usług AD FS, sam. Jeśli używasz usług AD FS w systemie Windows Server 2016 lub nowszym, pozostaje serwera w ustawieniach konfiguracji i zostanie wyświetlony ponownie przy kolejnym uruchomieniu zadania.

- **Czy mogę zaktualizować podzbiór serwerów w kolektywie serwerów przy użyciu nowego certyfikatu SSL?**

    Tak. Będzie można uruchamiać zadania **Aktualizuj certyfikat SSL** ponownie, aby zaktualizować pozostałe serwery. Na **zostanie przeprowadzona aktualizacja certyfikatu Wybierz serwery do obsługi protokołu SSL** strony, można sortować listę serwerów na **Data ważności protokołu SSL** aby łatwo uzyskiwać dostęp do serwerów, które nie są jeszcze zaktualizowane.

- **Po usunięciu serwera w poprzednim przebiegu, ale jej jest nadal są wyświetlane w trybie offline i wymienione na stronie serwery usług AD FS. Dlaczego jest w trybie offline serwer nadal istnieje, mimo on usunięty?**

    Usuwanie serwera z listy w programie Azure AD Connect nie powoduje usunięcia w konfiguracji usług AD FS. Program Azure AD Connect odwołuje się do żadnych informacji o farmie AD FS (Windows Server 2016 lub nowszy). Jeśli serwer jest nadal obecny w konfiguracji usług AD FS, będzie ono wyświetlane wstecz na liście.  

## <a name="next-steps"></a>Kolejne kroki

- [Program Azure AD Connect a federacja](how-to-connect-fed-whatis.md)
- [Zarządzania w usłudze Active Directory Federation Services i dostosowywania za pomocą usługi Azure AD Connect](how-to-connect-fed-management.md)

