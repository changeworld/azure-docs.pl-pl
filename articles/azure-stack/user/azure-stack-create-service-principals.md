---
title: Tworzenie jednostki usługi dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób tworzenia jednostki usługi używanej przy użyciu kontroli dostępu opartej na rolach w usłudze Azure Resource Manager w celu zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: da75d829ccb74f7be675501b601ad3e6375bfb70
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55813164"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Udzielić aplikacji dostępu do zasobów usługi Azure Stack, tworząc jednostki usługi

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Możesz udzielić dostępu aplikacji do zasobów usługi Azure Stack przez tworzenie usługi podmiotu zabezpieczeń, który używa usługi Azure Resource Manager. Jednostki usługi umożliwia delegowanie uprawnień określonej za pomocą [kontroli dostępu opartej na rolach](azure-stack-manage-permissions.md).

Najlepszym rozwiązaniem należy użyć nazwy główne usług dla aplikacji. Nazwy główne usług są preferowane w porównaniu do uruchamiania aplikacji przy użyciu własnych poświadczeń z następujących powodów:

* Możesz przypisywać uprawnienia do jednostki, które różnią się od uprawnień konta usługi. Zazwyczaj uprawnienia jednostki usługi są ograniczone do dokładnie co musi robić aplikacja.
* Nie trzeba zmienić poświadczenia aplikacji, w przypadku zmiany usługi roli lub odpowiedzialności.
* Certyfikat można użyć do zautomatyzowania uwierzytelnianie podczas uruchamiania skryptu instalacji nienadzorowanej.

## <a name="example-scenario"></a>Przykładowy scenariusz

Masz aplikację do zarządzania konfiguracji, która musi dodać do spisu zasobów platformy Azure przy użyciu usługi Azure Resource Manager. Można utworzyć jednostkę usługi i przypisz go do roli czytelnika. Ta rola zapewnia dostęp tylko do odczytu aplikacji do zasobów platformy Azure.

## <a name="getting-started"></a>Wprowadzenie

Wykonaj kroki w tym artykule przedstawiono wskazówki do:

* Tworzenie jednostki usługi dla aplikacji.
* Zarejestruj swoją aplikację i utworzyć klucz uwierzytelniania.
* Przypisywanie aplikacji do roli.

Sposób, w usłudze Active Directory jest skonfigurowany dla usługi Azure Stack Określa, jak utworzyć jednostkę usługi. Wybierz jedną z następujących opcji:

* Tworzenie usługi podmiotu zabezpieczeń dla [usługi Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Tworzenie usługi podmiotu zabezpieczeń dla [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Kroki przypisywanie nazwy głównej usługi do roli takie same dla usługi Azure AD i AD FS. Po utworzeniu nazwy głównej usługi, możesz [delegować uprawnienia](azure-stack-create-service-principals.md#assign-role-to-service-principal) , przypisując go do roli.

## <a name="create-service-principal-for-azure-ad"></a>Tworzenie jednostki usługi dla usługi Azure AD

Jeśli usługi Azure Stack używa usługi Azure AD do przechowywania tożsamości, należy można utworzyć jednostkę usługi za pomocą te same czynności co platformy Azure, przy użyciu witryny Azure portal.

>[!NOTE]
Sprawdź, czy masz [wymagane uprawnienia usługi Azure AD](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) przed rozpoczęciem tworzenia jednostki usługi.

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Aby utworzyć jednostkę usługi dla aplikacji:

1. Zaloguj się do konta platformy Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **Dodaj**.
3. Podaj nazwę i adres URL aplikacji. Wybierz opcję **aplikacji sieci Web / interfejs API** lub **natywnych** dla typu aplikacji, którą chcesz utworzyć. Po ustawieniu wartości, wybierz **Utwórz**.

### <a name="get-credentials"></a>Pobierz poświadczenia

Podczas logowania programowo, użyj Identyfikatora swojej aplikacji i klucza uwierzytelniania. Aby wyświetlić następujące wartości:

1. Z **rejestracje aplikacji** w usłudze Active Directory, wybierz swoją aplikację.

2. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Te aplikacje w aplikacji przykładowych **identyfikator klienta** przy odwoływaniu się do **identyfikator aplikacji**.

     ![Identyfikator aplikacji dla aplikacji](./media/azure-stack-create-service-principal/image12.png)
3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

4. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

>[!IMPORTANT]
Po zapisaniu klucza, klucz **wartość** jest wyświetlana. Zanotuj tę wartość, ponieważ nie można pobrać klucza później. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

![Ostrzeżenie wartości klucza dla klucza zapisane.](./media/azure-stack-create-service-principal/image15.png)

Ostatnim krokiem jest [przypisanie roli aplikacji](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Tworzenie jednostki usługi dla usług AD FS

Jeśli wdrożono usługę Azure Stack przy użyciu usług AD FS jako magazynu tożsamości, można użyć programu PowerShell dla następujących zadań:

* Tworzenie jednostki usługi.
* Przypisywanie nazwy głównej usługi do roli.
* Zaloguj się przy użyciu jednostki usługi tożsamości.

Aby uzyskać więcej informacji na temat tworzenia jednostki usługi, zobacz [Tworzenie jednostki usługi dla usług AD FS](../azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Przypisywanie nazwy głównej usługi do roli

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, rolę, która reprezentuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: wbudowane role](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Zakres roli można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład aplikacji za pomocą roli Czytelnik dla grupy zasobów oznacza, że aplikacja może odczytywać zasoby w grupie zasobów.

W przypadku przypisywania roli do jednostki usługi, wykonaj następujące kroki jako wskazówki.

1. W portalu usługi Azure Stack przejdź do poziomu zakresu, w których chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**.

2. Wybierz subskrypcję, której chcesz przypisać aplikację. W tym przykładzie jest subskrypcja programu Visual Studio Enterprise.

     ![Wybierz subskrypcję programu Visual Studio Enterprise dla przypisania](./media/azure-stack-create-service-principal/image16.png)

3. Wybierz **kontrola dostępu (IAM)** dla subskrypcji.

4. Wybierz **Dodaj przypisanie roli**.

5. Wybierz rolę, którą chcesz przypisać do aplikacji.

6. Wyszukaj aplikację, a następnie wybierz ją.

7. Wybierz **OK** zakończenie przypisanie roli. Możesz zobaczyć aplikację na liście Użytkownicy przypisani do roli dla tego zakresu.

Teraz, po utworzeniu nazwy głównej usługi i przypisaną rolę, aplikacja może uzyskiwać dostęp do zasobów usługi Azure Stack.

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)
