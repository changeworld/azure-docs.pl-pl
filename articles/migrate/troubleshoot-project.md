---
title: Rozwiązywanie problemów z projektami usługi Azure Migrate
description: Pomaga rozwiązywać problemy z tworzeniem projektów migracji platformy Azure i zarządzanie nimi.
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/01/2020
ms.openlocfilehash: 3b8c2f6ec33965317d2aaa23a36b6becff11a54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75725730"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Rozwiązywanie problemów z projektami usługi Azure Migrate

Ten artykuł ułatwia rozwiązywanie problemów podczas tworzenia projektów [migracji platformy Azure](migrate-services-overview.md) i zarządzania nimi.

## <a name="how-to-add-new-project"></a>Jak dodać nowy projekt?

W ramach subskrypcji można mieć wiele projektów migracji platformy Azure. [Dowiedz się, jak](how-to-add-tool-first-time.md) utworzyć projekt po raz pierwszy lub [dodać dodatkowe](create-manage-projects.md#create-additional-projects) projekty.

## <a name="what-azure-permissions-are-needed"></a>Jakie uprawnienia platformy Azure są potrzebne?

Aby utworzyć projekt migracji platformy Azure, potrzebujesz uprawnień współautora lub właściciela w ramach subskrypcji.

## <a name="cant-find-a-project"></a>Nie można znaleźć projektu

Znajdowanie istniejącego projektu migracji platformy Azure zależy od tego, czy używasz bieżącej czy starej wersji usługi Azure Migrate. [Postępuj zgodnie](create-manage-projects.md#find-a-project)z .


## <a name="cant-find-a-geography"></a>Nie można znaleźć geografii

Projekt migracji platformy Azure można utworzyć w [obsługiwanych regionach geograficznych.](migrate-support-matrix.md#supported-geographies) Należy zauważyć, że geografia projektu jest używana do przechowywania odnalezionych metadanych maszyny. Można również ocenić lub przeprowadzić migrację maszyn w innych lokalizacjach.

## <a name="what-are-vm-limits"></a>Co to są limity maszyn wirtualnych?

W jednym projekcie można ocenić do 35 000 maszyn wirtualnych VMware lub do 35 000 maszyn wirtualnych funkcji Hyper V. Projekt może zawierać zarówno maszyny wirtualne VMware, jak i maszyny wirtualne funkcji Hyper-V, aż do limitów oceny.

## <a name="can-i-upgrade-old-project"></a>Czy mogę uaktualnić stary projekt?

Nie można zaktualizować projektów z poprzedniej wersji usługi Azure Migrate. Należy [utworzyć nowy projekt](how-to-add-tool-first-time.md)i dodać do niego narzędzia.

## <a name="cant-create-a-project"></a>Nie można utworzyć projektu

Jeśli spróbujesz utworzyć projekt i napotkasz błąd wdrożenia:

- Spróbuj utworzyć projekt ponownie w przypadku, gdy jest to błąd przejściowy. W **wdrożeń**kliknij **ponownie wdrożyć,** aby spróbować ponownie.
- Sprawdź, czy masz uprawnienia współautora lub właściciela w subskrypcji.
- Jeśli wdrażasz w nowo dodanej lokalizacji geograficznej, poczekaj krótko i spróbuj ponownie.
- Jeśli zostanie wyświetlony błąd "Żądania muszą zawierać nagłówki tożsamości użytkownika", może to oznaczać, że nie masz dostępu do dzierżawy usługi Azure Active Directory (Azure AD) w organizacji. W takim przypadku:
    - Po dodaniu do dzierżawy usługi Azure AD po raz pierwszy otrzymasz zaproszenie e-mail do dołączenia do dzierżawy.
    - Zaakceptuj zaproszenie, które ma zostać dodane do dzierżawy.
    - Jeśli nie widzisz wiadomości e-mail, skontaktuj się z użytkownikiem z dostępem do dzierżawy i poproś go o [ponowne wysłanie zaproszenia.](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users)
    - Po otrzymaniu wiadomości e-mail z zaproszeniem otwórz ją i wybierz link, aby zaakceptować zaproszenie. Następnie wyloguj się z witryny Azure portal i zaloguj się ponownie. (odświeżanie przeglądarki nie będzie działać). Następnie można rozpocząć tworzenie projektu migracji.

## <a name="how-do-i-delete-a-project"></a>Jak usunąć projekt

[Postępuj zgodnie z tymi instrukcjami,](create-manage-projects.md#delete-a-project) aby usunąć projekt. Należy zauważyć, że po usunięciu projektu, zarówno projektu i metadanych o odnalezionych maszyn w projekcie są usuwane.

## <a name="added-tools-dont-show"></a>Dodane narzędzia nie są wyświetlane

Upewnij się, że masz wybrany odpowiedni projekt. W centrum migracji platformy Azure > **serwerów** lub w **bazach danych**kliknij pozycję **Zmień** obok pozycji **Migruj projekt (Zmień)** w prawym górnym rogu ekranu. Wybierz poprawną nazwę subskrypcji i projektu > **OK**. Strona powinna zostać odświeżona za pomocą dodanych narzędzi wybranego projektu.

## <a name="next-steps"></a>Następne kroki

Dodaj narzędzia [oceny](how-to-assess.md) lub [migracji](how-to-migrate.md) do projektów migracji platformy Azure.