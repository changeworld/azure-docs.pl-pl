---
title: Usługa Azure AD Connect — aktualizowanie certyfikatu TLS/SSL dla farmy usług AD FS | Dokumenty firmy Microsoft
description: Ten dokument zawiera szczegółowe informacje o krokach, aby zaktualizować certyfikat TLS/SSL farmy usług AD FS przy użyciu usługi Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cc768162d98402fe52b52b2826a9dbf2840a581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331734"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizowanie certyfikatu TLS/SSL dla farmy usług federacyjnych Active Directory (AD FS)

## <a name="overview"></a>Omówienie
W tym artykule opisano, jak za pomocą usługi Azure AD Connect można zaktualizować certyfikat TLS/SSL dla farmy usług federacyjnych Active Directory (AD FS). Za pomocą narzędzia Azure AD Connect można łatwo zaktualizować certyfikat TLS/SSL dla farmy usług AD FS, nawet jeśli wybrana metoda logowania użytkownika nie jest usługą AD FS.

Całą operację aktualizowania certyfikatu TLS/SSL dla farmy usług AD FS można wykonać na wszystkich serwerach federacyjnych i serwerach WAP (Web Application Proxy) w trzech prostych krokach:

![Trzy kroki](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Aby dowiedzieć się więcej o certyfikatach używanych przez usługi AD FS, zobacz [Opis certyfikatów używanych przez usługi AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Wymagania wstępne

* **Farma usług AD FS**: Upewnij się, że farma usług AD FS jest oparta na systemie Windows Server 2012 R2 lub nowszej.
* **Usługa Azure AD Connect**: Upewnij się, że wersja usługi Azure AD Connect jest 1.1.553.0 lub nowsza. Użyjesz zadania **Aktualizuj certyfikat SSL usługi AD FS**.

![Aktualizowanie zadania protokołu TLS](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: Podaj informacje o farmie usług AD FS

Usługa Azure AD Connect próbuje automatycznie uzyskać informacje o farmie usług AD FS przez:
1. Wykonywanie zapytań o informacje o farmie z usług AD FS (Windows Server 2016 lub nowszych).
2. Odwoływanie się do informacji z poprzednich uruchomień, które są przechowywane lokalnie za pomocą usługi Azure AD Connect.

Można zmodyfikować listę serwerów, które są wyświetlane, dodając lub usuwając serwery, aby odzwierciedlić bieżącą konfigurację farmy usług AD FS. Po połączeniu informacji o serwerze usługa Azure AD Connect wyświetla łączność i bieżący stan certyfikatu TLS/SSL.

![Informacje o serwerze usług AD FS](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Jeśli lista zawiera serwer, który nie jest już częścią farmy usług AD FS, kliknij przycisk **Usuń,** aby usunąć serwer z listy serwerów w farmie usług AD FS.

![Serwer w trybie offline na liście](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Usunięcie serwera z listy serwerów dla farmy usług AD FS w usłudze Azure AD Connect jest operacją lokalną i aktualizowaniem informacji dla farmy usług AD FS, którą usługa Azure AD Connect przechowuje lokalnie. Usługa Azure AD Connect nie modyfikuje konfiguracji w usługach AD FS, aby odzwierciedlić zmianę.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Krok 2: Podaj nowy certyfikat TLS/SSL

Po potwierdzeniu informacji o serwerach farmy usług AD FS usługa Azure AD Connect prosi o nowy certyfikat TLS/SSL. Podaj chroniony hasłem certyfikat PFX, aby kontynuować instalację.

![Certyfikat TLS/SSL](./media/how-to-connect-fed-ssl-update/certificate.png)

Po podasz certyfikat usługi Azure AD Connect przechodzi przez szereg wymagań wstępnych. Sprawdź certyfikat, aby upewnić się, że certyfikat jest poprawny dla farmy usług AD FS:

-   Nazwa podmiotu/alternatywna nazwa podmiotu certyfikatu jest taka sama jak nazwa usługi federacyjnej lub certyfikat wieloznaczny.
-   Certyfikat jest ważny dłużej niż 30 dni.
-   Łańcuch zaufania certyfikatów jest prawidłowy.
-   Certyfikat jest chroniony hasłem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Wybierz serwery do aktualizacji

W następnym kroku wybierz serwery, na których certyfikat TLS/SSL musi być aktualizowany. Nie można wybrać serwerów w trybie offline dla aktualizacji.

![Wybieranie serwerów do aktualizacji](./media/how-to-connect-fed-ssl-update/selectservers.png)

Po zakończeniu konfiguracji usługa Azure AD Connect wyświetla komunikat wskazujący stan aktualizacji i udostępnia opcję weryfikacji logowania do usług AD FS.

![Ukończono konfigurację](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Często zadawane pytania

* **Jaka powinna być nazwa podmiotu certyfikatu dla nowego certyfikatu TLS/SSL usługi AD FS?**

    Usługa Azure AD Connect sprawdza, czy nazwa podmiotu/alternatywna nazwa podmiotu certyfikatu zawiera nazwę usługi federacyjnej. Jeśli na przykład nazwa usługi federacyjnej jest fs.contoso.com, nazwa podmiotu/alternatywna nazwa podmiotu musi zostać fs.contoso.com.  Akceptowane są również certyfikaty symboli wieloznacznych.

* **Dlaczego ponownie na stronie serwera WAP jest proszony o podanie poświadczeń?**

    Jeśli poświadczenia podawania do łączenia się z serwerami usług AD FS nie mają również uprawnień do zarządzania serwerami WAP, usługa Azure AD Connect prosi o poświadczenia, które mają uprawnienia administracyjne na serwerach WAP.

* **Serwer jest wyświetlany w trybie offline. Co należy zrobić?**

    Usługa Azure AD Connect nie może wykonać żadnej operacji, jeśli serwer jest w trybie offline. Jeśli serwer jest częścią farmy usług AD FS, sprawdź łączność z serwerem. Po rozwiązaniu problemu naciśnij ikonę odświeżania, aby zaktualizować stan kreatora. Jeśli serwer był częścią farmy wcześniej, ale teraz już nie istnieje, kliknij przycisk **Usuń,** aby usunąć go z listy serwerów, które obsługiwa usługi Azure AD Connect. Usunięcie serwera z listy w usłudze Azure AD Connect nie zmienia samej konfiguracji usług AD FS. Jeśli używasz usług AD FS w systemie Windows Server 2016 lub nowszym, serwer pozostaje w ustawieniach konfiguracji i będzie wyświetlany ponownie przy następnym uruchomieniu zadania.

* **Czy mogę zaktualizować podzbiór serwerów farmy za pomocą nowego certyfikatu TLS/SSL?**

    Tak. Zawsze można ponownie uruchomić zadanie **Aktualizuj certyfikat SSL,** aby zaktualizować pozostałe serwery. Na stronie **Wybierz serwery dla aktualizacji certyfikatów SSL** można sortować listę serwerów w **dniu wygaśnięcia SSL,** aby łatwo uzyskać dostęp do serwerów, które nie zostały jeszcze zaktualizowane.

* **Usunąłem serwer w poprzednim uruchomieniu, ale nadal jest wyświetlany w trybie offline i wymieniony na stronie Serwery usług AD FS. Dlaczego serwer w trybie offline nadal istnieje nawet po jego usunięciu?**

    Usunięcie serwera z listy w usłudze Azure AD Connect nie powoduje usunięcia go w konfiguracji usług AD FS. Usługa Azure AD Connect odwołuje się do usług AD FS (Windows Server 2016 lub nowszych) w celu uzyskania jakichkolwiek informacji o farmie. Jeśli serwer jest nadal obecny w konfiguracji usług AD FS, zostanie wymieniony z powrotem na liście.  

## <a name="next-steps"></a>Następne kroki

- [Usługa Azure AD Connect i federacja](how-to-connect-fed-whatis.md)
- [Zarządzanie usługami federacyjnymi Active Directory i dostosowywanie za pomocą usługi Azure AD Connect](how-to-connect-fed-management.md)

