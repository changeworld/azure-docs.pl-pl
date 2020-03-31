---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903702"
---
Ale jeśli chcesz udostępniać obrazy poza dzierżawy platformy Azure, na dużą skalę, należy utworzyć rejestrację aplikacji w celu ułatwienia udostępniania.  Korzystanie z rejestracji aplikacji może włączyć bardziej złożone scenariusze udostępniania, takie jak: 

* Zarządzanie udostępnionymi obrazami, gdy jedna firma nabywa inną, a infrastruktura platformy Azure jest rozłożona na oddzielnych dzierżaw. 
* Partnerzy platformy Azure zarządzają infrastrukturą platformy Azure w imieniu swoich klientów. Dostosowywanie obrazów odbywa się w dzierżawie partnerów, ale wdrożenia infrastruktury nastąpi w dzierżawie klienta. 


## <a name="create-the-app-registration"></a>Tworzenie rejestracji aplikacji

Utwórz rejestrację aplikacji, która będzie używana przez obu dzierżawców do udostępniania zasobów galerii obrazów.
1. Otwórz [rejestracje aplikacji (wersja zapoznawcza) w witrynie Azure portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Wybierz **pozycję Nowa rejestracja** z menu u góry strony.
1. W **nazwie**wpisz *myGalleryApp*.
1. W **obszarze Obsługiwane typy kont**wybierz pozycję Konta w dowolnym katalogu **organizacyjnym i osobistych kontach Microsoft**.
1. W **obszarze Przekieruj identyfikator URI**wpisz, *https://www.microsoft.com* a następnie wybierz pozycję **Zarejestruj**. Po utworzeniu rejestracji aplikacji zostanie otwarta strona przeglądu.
1. Na stronie przegląd skopiuj **identyfikator aplikacji (klienta)** i zapisz do późniejszego użycia.   
1. Wybierz **pozycję Certyfikaty & wpisy tajne**, a następnie wybierz pozycję Nowy klucz tajny **klienta**.
1. W **opisie**wpisz Klucz *tajny aplikacji Shared image gallery cross-tenant*.
1. W **obszarze Wygasa**pozostaw wartość domyślną w ciągu **1 roku,** a następnie wybierz pozycję **Dodaj**.
1. Skopiuj wartość klucza tajnego i zapisz go w bezpiecznym miejscu. Nie można go pobrać po opuszczeniu strony.


Nadaj aplikacji uprawnienia do rejestracji do korzystania z galerii obrazów udostępnionych.
1. W witrynie Azure portal wybierz Galerię obrazów udostępnionych, które chcesz udostępnić innej dzierżawie.
1. Wybierz **pozycję Wybierz kontrolka dostępu (IAM)** i w obszarze **Dodaj przypisanie roli** wybierz pozycję *Dodaj*. 
1. W obszarze **Rola**wybierz **pozycję Czytelnik**.
1. W obszarze **Przypisywanie dostępu do:** pozostaw to jako **użytkownik, grupa lub podmiot usługi Azure AD**.
1. W obszarze **Wybierz**wpisz *myGalleryApp* i wybierz ją, gdy pojawi się na liście. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.


## <a name="give-tenant-2-access"></a>Nadaj najemcy 2 dostęp

Zapewnij dzierżawcy 2 dostęp do aplikacji, żądając logowania za pomocą przeglądarki. Zastąp * \<identyfikator Tenant2>* identyfikatorem dzierżawy dla dzierżawy, której chcesz udostępnić galerię obrazów. Zamień * \<identyfikator aplikacji (klienta)>* identyfikatorem aplikacji utworzonej rejestracji aplikacji. Po wykonaniu dokonywania zamienników, wklej adres URL do przeglądarki i postępuj zgodnie z monitami logowania, aby zalogować się do dzierżawy 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

W [witrynie Azure portal](https://portal.azure.com) zaloguj się jako dzierżawca 2 i dać dostęp do rejestracji aplikacji do grupy zasobów, w którym chcesz utworzyć maszynę wirtualną.

1. Wybierz grupę zasobów, a następnie wybierz **pozycję Kontrola dostępu (IAM)**. W obszarze **Dodaj przypisanie roli** wybierz pozycję **Dodaj**. 
1. W obszarze **Rola**wpisz **współautor**.
1. W obszarze **Przypisywanie dostępu do:** pozostaw to jako **użytkownik, grupa lub podmiot usługi Azure AD**.
1. W obszarze **Wybierz** *typ myGalleryApp* wybierz ją, gdy pojawi się na liście. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

> [!NOTE]
> Musisz poczekać, aż wersja obrazu całkowicie zakończyć budowa i replikowanie, zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu.

