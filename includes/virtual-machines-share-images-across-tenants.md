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
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145888"
---
Udostępnione galerie obrazów pozwalają udostępniania obrazów przy użyciu funkcji RBAC. RBAC można użyć do udostępniania obrazów w ramach dzierżawy, a nawet do osób spoza Twojej dzierżawy. Ale jeśli chcesz udostępnić obrazów spoza Twojej dzierżawy platformy Azure na dużą skalę, należy utworzyć rejestrację aplikacji w taki sposób, aby usprawnić udostępnianie.  Korzystanie z rejestracji aplikacji można włączyć bardziej złożonych scenariuszy do udostępniania, takie jak: 

* Zarządzanie udostępnionymi obrazów, gdy jedna spółka przejmuje innego i infrastruktury platformy Azure jest rozmieszczony na oddzielnych dzierżaw. 
* Partnerzy portalu Azure Zarządzaj infrastrukturą platformy Azure w imieniu swoich klientów. Dostosowanie obrazów jest wykonywane w ramach dzierżawy partnerów, ale wdrożenia infrastruktury będą występować w dzierżawie klienta. 


## <a name="create-the-app-registration"></a>Tworzenie rejestracji aplikacji

Utwórz rejestrowanie aplikacji, które będą używane przez oba dzierżaw do udostępniania zasobów galerii obrazów.
1. Otwórz [rejestracje aplikacji (wersja zapoznawcza) w witrynie Azure portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Wybierz **nowej rejestracji** menu w górnej części strony.
1. W **nazwa**, typ *myGalleryApp*.
1. W **obsługiwane typy kont**, wybierz opcję **kont w dowolnym katalogu organizacji i osobistych kont Microsoft**.
1. W **identyfikator URI przekierowania**, typ *https://www.microsoft.com* , a następnie wybierz **zarejestrować**. Po utworzeniu rejestracji aplikacji zostanie otwarta strona przeglądu.
1. Na stronie Przegląd Skopiuj **identyfikator aplikacji (klienta)** i zapisać do użytku w przyszłości.   
1. Wybierz **certyfikaty i klucze tajne**, a następnie wybierz pozycję **nowy wpis tajny klienta**.
1. W **opis**, typ *wspólny klucz tajny aplikacji w wielu dzierżawach galerii dla obrazu*.
1. W **Expires**, pozostaw wartość domyślną **w 1 rok** , a następnie wybierz **Dodaj**.
1. Skopiuj wartość klucza tajnego i zapisz go w bezpiecznym miejscu. Nie można go odzyskać, gdy opuścić tę stronę.


Nadaj uprawnienia rejestracji aplikacji na korzystanie z galerii obrazów udostępnionych.
1. W witrynie Azure portal wybierz udostępnione galerii obrazów, którą chcesz udostępnić za pomocą innej dzierżawy.
1. Wybierz **wybierz kontroli dostępu (IAM)** , a następnie w obszarze **Dodaj przypisanie roli** wybierz *Dodaj*. 
1. W obszarze **roli**, wybierz opcję **czytnika**.
1. W obszarze **Przypisz dostęp do:** , pozostaw to jako **użytkownika, grupy lub jednostki usługi Azure AD**.
1. W obszarze **wybierz**, typ *myGalleryApp* i zaznacz je, gdy zostanie ona wyświetlona na liście. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.


## <a name="give-tenant-2-access"></a>Udostępnij 2 dzierżawy

Udostępnij 2 dzierżawy do aplikacji, wysyłając żądanie logowania za pomocą przeglądarki. Zastąp *<Tenant2 ID>* dla dzierżawy, który chcesz udostępnić galerii obrazów z identyfikatorem dzierżawy. Zastąp *< identyfikator aplikacji (klienta) >* z Identyfikatorem aplikacji w rejestracji aplikacji utworzony. Po zakończeniu wprowadzania zamiany Wklej adres URL do przeglądarki i postępuj zgodnie z instrukcjami logowania, aby zalogować się do 2 dzierżawy.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

W [witryny Azure portal](https://portal.azure.com) Zaloguj się jako dzierżawca, 2 i udzielić dostępu rejestracji aplikacji do grupy zasobów, w której chcesz utworzyć maszynę Wirtualną.

1. Wybierz grupę zasobów, a następnie wybierz pozycję **kontrola dostępu (IAM)** . W obszarze **Dodaj przypisanie roli** wybierz **Dodaj**. 
1. W obszarze **roli**, typ **Współautor**.
1. W obszarze **Przypisz dostęp do:** , pozostaw to jako **użytkownika, grupy lub jednostki usługi Azure AD**.
1. W obszarze **wybierz** typu *myGalleryApp* następnie wybierz ją, jeśli zostanie ona wyświetlona na liście. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**.

> [!NOTE]
> Musisz czekać na wersję obrazu do całkowitego zakończenia są zbudowane i replikowane korzystać z tego samego obrazu zarządzanego, aby utworzyć inną wersję obrazu.

