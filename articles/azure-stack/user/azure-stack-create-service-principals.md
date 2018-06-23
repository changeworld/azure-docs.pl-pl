---
title: Tworzenie nazwy głównej usługi Azure stosu | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia nazwy głównej usługi, który może służyć z kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/21/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 3c9f114c2844021d515765888aa19f18a0adc10b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320758"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Udzielić dostępu aplikacji do zasobów Azure stosu przez utworzenie nazwy główne usług

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Tworząc usługę podmiotu zabezpieczeń, która używa usługi Azure Resource Manager pozwala aplikacji uzyskiwać dostęp do zasobów Azure stosu. Nazwy głównej usługi umożliwia delegowanie uprawnień określonego za pomocą [kontroli dostępu opartej na rolach](azure-stack-manage-permissions.md).

Najlepszym rozwiązaniem należy użyć nazwy główne usług dla aplikacji. Nazwy główne usług są preferowane w porównaniu do uruchomienia aplikacji za pomocą własnych poświadczenia z następujących powodów:

* Można przypisać uprawnienia do podmiotu zabezpieczeń, które są inne niż uprawnień konta usługi. Zazwyczaj uprawnienia nazwy głównej usługi są ograniczone do dokładnie co aplikacja musi wykonać.
* Nie masz umożliwia zmianę poświadczeń aplikacji z roli lub obowiązki zmienić.
* Certyfikat służy do automatyzacji procesu uwierzytelniania podczas uruchamiania skryptu instalacji nienadzorowanej.

## <a name="example-scenario"></a>Przykładowy scenariusz

Masz aplikacji do zarządzania konfiguracji, który musi spisu zasobów platformy Azure przy użyciu usługi Azure Resource Manager. Można utworzyć usługę podmiotu zabezpieczeń i przypisać rolę czytelnika. Ta rola daje aplikacji dostęp tylko do odczytu do zasobów platformy Azure.

## <a name="getting-started"></a>Wprowadzenie

Wykonaj kroki w tym artykule jako przewodnik dotyczący:

* Tworzenie nazwy głównej usługi dla aplikacji.
* Rejestrowanie aplikacji i utworzyć klucz uwierzytelniania.
* Przypisywanie aplikacji do roli.

Sposób usługi Active Directory jest skonfigurowany dla stosu Azure określa sposób tworzenia nazwy głównej usługi. Wybierz jedną z następujących opcji:

* Utwórz usługę podmiotu zabezpieczeń dla [usługi Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Utwórz usługę podmiotu zabezpieczeń dla [Active Directory Federation Services (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Kroki dotyczące przypisywania nazwy głównej usługi do roli takie same dla usługi Azure AD i usług AD FS. Po utworzeniu nazwy głównej usługi można [delegować uprawnienia](azure-stack-create-service-principals.md#assign-role-to-service-principal) przez przypisanie go do roli.

## <a name="create-a-service-principal-for-azure-ad"></a>Tworzenie nazwy głównej usługi dla usługi Azure AD

Jeśli stosu Azure używa usługi Azure AD jako magazynu tożsamości, można utworzyć usługę podmiotu zabezpieczeń, korzystając z tej samej procedury jak Azure przy użyciu portalu Azure.

>[!NOTE]
Sprawdź, czy masz [wymagane uprawnienia usługi Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) przed rozpoczęciem tworzenia nazwy głównej usługi.

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

Aby utworzyć nazwy głównej usługi dla aplikacji:

1. Zaloguj się do konta platformy Azure za pośrednictwem [portalu Azure](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **rejestracji aplikacji** > **Dodaj**.
3. Podaj nazwę i adres URL aplikacji. Wybierz opcję **aplikacji sieci Web / interfejs API** lub **natywnego** dla typu aplikacji, w którym chcesz utworzyć. Po ustawieniu wartości, wybierz **Utwórz**.

### <a name="get-credentials"></a>Pobierz poświadczenia

Podczas logowania się w programowo, należy użyć Identyfikatora aplikacji i klucz uwierzytelniania. Aby wyświetlić te wartości:

1. Z **rejestracji aplikacji** w usłudze Active Directory, wybierz aplikację.

2. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Aplikacje w [przykładowe aplikacje](#sample-applications) użyj **identyfikator klienta** podczas odwoływania się do **identyfikator aplikacji**.

     ![Identyfikator aplikacji dla aplikacji](./media/azure-stack-create-service-principal/image12.png)
3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

4. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

>[!IMPORTANT]
Po zapisaniu klucz **wartość** jest wyświetlany. Zapisz tę wartość, ponieważ nie można pobrać klucza później. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

![Ostrzeżenie wartość klucza dla klucza zapisane.](./media/azure-stack-create-service-principal/image15.png)

Ostatnim krokiem jest [przypisywanie roli aplikacji](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Tworzenie nazwy głównej usługi dla usług AD FS

Jeśli wdrożono stosu Azure jako magazynu tożsamości za pomocą usług AD FS, można użyć programu PowerShell dla następujących zadań:

* Tworzenie nazwy głównej usługi.
* Przypisz nazwę główną usługi do roli.
* Zaloguj się przy użyciu nazwy głównej usługi tożsamości.

Aby uzyskać więcej informacji na temat tworzenia nazwy głównej usługi, zobacz [Tworzenie nazwy głównej usługi dla usług AD FS](../azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Przypisz nazwę główną usługi do roli

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, które roli reprezentuje odpowiednich uprawnień dla aplikacji. Aby dowiedzieć się więcej o dostępnych ról, zobacz [RBAC: Built in Roles](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Zakres roli można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższe poziomy zakresu. Na przykład aplikację z rolę czytelnika dla grupy zasobów oznacza, że aplikację można odczytać zasobów w grupie zasobów.

Wykonaj następujące kroki jako przewodnik dla przypisania ról nazwy głównej usługi.

1. W portalu Azure stosu przejdź na poziom zakresu ma zostać przypisany do aplikacji. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**.

2. Wybierz subskrypcję, aby przypisać aplikację. W tym przykładzie subskrypcja jest Visual Studio Enterprise.

     ![Wybierz subskrypcję programu Visual Studio Enterprise do przypisania](./media/azure-stack-create-service-principal/image16.png)

3. Wybierz **kontroli dostępu (IAM)** dla subskrypcji.

     ![Wybierz kontroli dostępu](./media/azure-stack-create-service-principal/image17.png)

4. Wybierz pozycję **Dodaj**.

5. Wybierz rolę, którą chcesz przypisać do aplikacji.

6. Wyszukaj aplikację i zaznacz go.

7. Wybierz **OK** na zakończenie przypisanie roli. Widać aplikacji na liście użytkowników przypisanych do danej roli dla tego zakresu.

Teraz, utworzeniu nazwy głównej usługi i przypisaną rolę, aplikacji dostęp do zasobów Azure stosu.

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)
