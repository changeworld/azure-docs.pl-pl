---
title: Azure AD Connect - zaktualizuj certyfikat protokołu SSL dla farmy usług AD FS | Dokumentacja firmy Microsoft
description: Szczegóły tego dokumentu kroki, aby zaktualizować certyfikat SSL farmy usług AD FS przy użyciu usługi Azure AD Connect.
services: active-directory
keywords: programu Azure ad connect, aktualizacji ssl usług AD FS, aktualizacja certyfikatu usług AD FS, zmienianie certyfikatu usług AD FS, nowego certyfikatu usług AD FS, certyfikat usług AD FS aktualizacji usług AD FS certyfikat ssl, aktualizacja AD FS certyfikat ssl, skonfigurować certyfikat ssl usług AD FS, usługi AD FS, ssl, certyfikatu, certyfikat komunikacji usługi AD FS, federation aktualizacji, konfigurowanie Federacji, aad connect
authors: anandyadavmsft
manager: mtillman
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.component: hybrid
ms.author: anandy
ms.custom: seohack1
ms.openlocfilehash: d657725b7bfb75a118c1d8bbbe701c1c121b1ef2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595006"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizuj certyfikat protokołu SSL dla farmę serwerów usługi Active Directory Federation Services (AD FS)

## <a name="overview"></a>Przegląd
W tym artykule opisano, jak Azure AD Connect umożliwia zaktualizowanie certyfikatu SSL dla farmę serwerów usługi Active Directory Federation Services (AD FS). Narzędzie Azure AD Connect pozwalają łatwo aktualizować certyfikat SSL do farmy usług AD FS, nawet jeśli nie jest użytkownik logowania wybrano metodę usług AD FS.

Można wykonać całą operację aktualizowania certyfikat protokołu SSL dla farmy usług AD FS we wszystkich federacyjnych i serwerach Proxy aplikacji sieci Web (WAP) w trzy proste kroki:

![Trzy kroki](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Aby dowiedzieć się więcej o certyfikatach, które są używane przez usługi AD FS, zobacz [Opis certyfikatów używane przez usługi AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Wymagania wstępne

* **Farmy usług AD FS**: Upewnij się, że farmę usług AD FS jest systemem Windows Server 2012 R2 lub nowszym.
* **Azure AD Connect**: Upewnij się, że wersja programu Azure AD Connect jest 1.1.553.0 lub nowszej. Użyjesz zadania **aktualizacja AD FS certyfikat**.

![Zadanie aktualizacji protokołu SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: Podaj informacje farmy usług AD FS

Azure AD Connect podejmuje próbę automatycznie przez Uzyskaj informacje na temat farmy usług AD FS:
1. Podczas badania informacji farmy z usług AD FS (Windows Server 2016 lub nowszy).
2. Odwołanie do informacje z poprzedniego działa, które są przechowywane lokalnie z programem Azure AD Connect.

Umożliwia zmodyfikowanie listy serwerów, które są wyświetlane przez dodawanie lub usuwanie serwerów w celu odzwierciedlenia bieżącej konfiguracji farmy usług AD FS. Jak podano informacje o serwerze, Azure AD Connect Wyświetla łączności i bieżący stan certyfikatu SSL.

![Informacje o serwerze programu AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Jeśli lista zawiera serwer, który nie jest już częścią farmy usług AD FS, kliknij przycisk **Usuń** można usunąć serwera na liście serwerów w farmie serwerów usług AD FS.

![W trybie offline serwer na liście](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Usuwanie serwera z listy serwerów dla farmy usług AD FS w programie Azure AD Connect jest operacją lokalnego i aktualizuje informacje o farmy usług AD FS, które Azure AD Connect obsługuje lokalnie. Azure AD Connect nie modyfikuje konfiguracji w usługach AD FS w celu odzwierciedlenia zmian.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Krok 2: Podaj nowy certyfikat SSL

Po potwierdzeniu informacji na temat usług AD FS farmy serwerów usługi Azure AD Connect monituje o podanie nowego certyfikatu SSL. Podaj certyfikat PFX chroniony hasłem, aby kontynuować instalację.

![Certyfikat SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

Po podaniu certyfikatu Azure AD Connect przechodzi przez szereg wymagań wstępnych. Sprawdź certyfikat, aby upewnić się, że certyfikat jest poprawny dla farmy usług AD FS:

-   Nazwa podmiotu nazwy/alternatywny podmiotu certyfikatu jest ona taka sama jak nazwa usługi federacyjnej lub jest certyfikat uniwersalny.
-   Certyfikat jest ważny przez dłużej niż 30 dni.
-   Łańcuch zaufania certyfikatów jest nieprawidłowy.
-   Certyfikat jest chroniony hasłem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Wybierz serwery aktualizacji

W następnym kroku wybierz serwery, które wymagają certyfikatów SSL, zaktualizowane. Nie można wybrać serwery, które są w trybie offline dla aktualizacji.

![Wybierz serwery, aby zaktualizować](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Po zakończeniu konfigurowania Azure AD Connect wyświetla komunikat, który wskazuje stan aktualizacji i udostępnia opcję, aby sprawdzić logowanie usług AD FS.

![Ukończono konfigurację](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Często zadawane pytania

* **Co powinna być nazwą podmiotu certyfikatu dla nowego certyfikatu SSL usług AD FS?**

    Azure AD Connect sprawdza, czy nazwa podmiotu nazwy/alternatywny podmiotu certyfikatu zawiera nazwę usługi federacyjnej. Na przykład jeśli nazwa usługi federacyjnej jest fs.contoso.com, nazwa podmiotu nazwy/alternatywny podmiotu musi być fs.contoso.com.  Certyfikatów z symbolami wieloznacznymi również są akceptowane.

* **Dlaczego pojawia się pytanie o poświadczenia ponownie na stronie serwer proxy?**

    Jeśli poświadczenia podane w celu nawiązania serwerów usług AD FS również nie ma uprawnień do zarządzania serwerami proxy, Azure AD Connect zapyta o poświadczenia, które mają uprawnienia administracyjne na serwerów proxy.

* **Serwer jest wyświetlany w trybie offline. Co zrobić?**

    Azure AD Connect nie może wykonać żadnej operacji, jeśli serwer jest w trybie offline. Jeżeli serwer jest częścią farmy usług AD FS, sprawdź łączność z serwerem. Po problem został rozwiązany, naciśnij ikonę odświeżania, aby zaktualizować stan w kreatorze. Jeśli serwer był częścią farmy wcześniej, ale teraz już nie istnieje, kliknij przycisk **Usuń** go usunąć z listy serwerów, że Azure AD Connect obsługuje. Usuwanie serwera z listy w programie Azure AD Connect nie zmieni konfigurację usług AD FS samej siebie. Jeśli korzystasz z usług AD FS w systemie Windows Server 2016 lub nowszym, pozostaje serwera w ustawieniach konfiguracji i pojawi się ponownie przy następnym uruchomieniu zadania.

* **Można aktualizować podzbiór serwerów w kolektywie serwerów przy użyciu nowego certyfikatu SSL?**

    Tak. Będzie można uruchamiać zadania **zaktualizuj certyfikat SSL** ponownie, aby zaktualizować pozostałych serwerów. Na **wybierz serwery protokołu SSL certyfikatów aktualizacji** strony, można sortować listę serwerów na **datę wygaśnięcia SSL** aby łatwo uzyskiwać dostęp do serwerów, które nie są jeszcze zaktualizowana.

* **Po usunięciu serwera w poprzedniego działania, ale jego jest nadal wyświetlane jako w trybie offline i listy na stronie serwery usług AD FS. Dlaczego jest w trybie offline serwer nadal istnieje, nawet po usunięciu go?**

    Usuwanie serwera z listy w programie Azure AD Connect nie powoduje usunięcia w konfiguracji usług AD FS. Azure AD Connect odwołuje się do usług AD FS (Windows Server 2016 lub nowszego) żadnych informacji o farmy. Jeśli serwer jest nadal znajdują się w konfiguracji usług AD FS, będzie wyświetlane w liście.  

## <a name="next-steps"></a>Kolejne kroki

- [Program Azure AD Connect a federacja](active-directory-aadconnectfed-whatis.md)
- [Dostosowywanie z programem Azure AD Connect i zarządzania w usłudze Active Directory Federation Services](active-directory-aadconnect-federation-management.md)
