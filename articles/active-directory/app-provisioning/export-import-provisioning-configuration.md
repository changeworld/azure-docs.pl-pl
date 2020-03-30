---
title: Wyeksportuj konfigurację inicjowania obsługi administracyjnej i przywdrowuj do znanego stanu dobrego do odzyskiwania po awarii.| Dokumenty firmy Microsoft
description: Dowiedz się, jak wyeksportować konfigurację inicjowania obsługi administracyjnej i przywrócić do znanego dobrego stanu odzyskiwania po awarii.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a92a40a5fe3067cf96d3c742102c9ca66078cd5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051312"
---
# <a name="export-your-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Eksportowanie konfiguracji inicjowania obsługi administracyjnej i przywracanie do znanego dobrego stanu

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Eksportowanie i importowanie konfiguracji inicjowania obsługi administracyjnej z witryny Azure Portal

### <a name="how-can-i-export-my-provisioning-configuration"></a>Jak mogę wyeksportować konfigurację inicjowania obsługi administracyjnej?
Aby wyeksportować konfigurację:
1. W [witrynie Azure portal](https://portal.azure.com/)po lewej stronie panelu nawigacji wybierz pozycję **Azure Active Directory**.
2. W okienku **usługi Azure Active Directory** wybierz **aplikacje enterprise** i wybierz aplikację.
3. W lewym okienku nawigacji wybierz pozycję **Inicjując inicjowanie obsługi administracyjnej**. Na stronie konfiguracji inicjowania obsługi administracyjnej kliknij **mapowania atrybutów,** a następnie **pokaż opcje zaawansowane**i na koniec **przejrzyj schemat.** Spowoduje to, że przejdziesz do edytora schematów. 
5. Kliknij pobierz na pasku poleceń w górnej części strony, aby pobrać schemat.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Odzyskiwanie po awarii - powrót do znanego dobrego stanu
Eksportowanie i zapisywanie konfiguracji umożliwia przywrócenie poprzedniej wersji konfiguracji. Zaleca się eksportowanie konfiguracji inicjowania obsługi administracyjnej i zapisywanie jej do późniejszego użycia w dowolnym momencie zmiany mapowań atrybutów lub filtrów zakresu. Wszystko, co musisz zrobić, to otworzyć plik JSON, który został pobrany w powyższych krokach, skopiować całą zawartość pliku JSON, zastąpić całą zawartość ładunku JSON w edytorze schematów, a następnie zapisać. Jeśli istnieje aktywny cykl inicjowania obsługi administracyjnej, zostanie ukończony, a następny cykl użyje zaktualizowanego schematu. Następny cykl będzie również początkowym cyklem, który ponownie oceni każdego użytkownika i grupę na podstawie nowej konfiguracji. Podczas wycofywania poprzedniej konfiguracji należy wziąć pod uwagę następujące kwestie:
* Użytkownicy zostaną ponownie ocenione, aby ustalić, czy powinny one być w zakresie. Jeśli filtry zakresu zostały zmienione, użytkownik nie jest już w zakresie, zostaną one wyłączone. Chociaż jest to pożądane zachowanie w większości przypadków, istnieją czasy, w których można zapobiec i można użyć [funkcji pomijania usuwania zakresu.](https://docs.microsoft.com/azure/active-directory/app-provisioning/skip-out-of-scope-deletions) 
* Zmiana konfiguracji inicjowania obsługi administracyjnej powoduje ponowne uruchomienie usługi i wyzwala [początkowy cykl](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#provisioning-cycles-initial-and-incremental).


## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Eksportowanie i importowanie konfiguracji inicjowania obsługi administracyjnej przy użyciu interfejsu API programu Microsoft Graph
Za pomocą interfejsu API programu Microsoft Graph i Eksploratora programu Microsoft Graph można wyeksportować mapowania atrybutów i schematu inicjowania obsługi administracyjnej użytkownika do pliku JSON i zaimportować go z powrotem do usługi Azure AD. Można również użyć kroki przechwycone w tym miejscu, aby utworzyć kopię zapasową konfiguracji inicjowania obsługi administracyjnej. 

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Krok 1: Pobieranie identyfikatora głównego usługi aplikacji inicjowania obsługi administracyjnej (identyfikator obiektu)

1. Uruchom [witrynę Azure portal](https://portal.azure.com)i przejdź do sekcji Właściwości aplikacji inicjującej inicjowanie obsługi administracyjnej. Na przykład, jeśli chcesz wyeksportować program Workday do mapowania *aplikacji inicjowania obsługi administracyjnej użytkowników usługi AD,* przejdź do sekcji Właściwości tej aplikacji. 
1. W sekcji Właściwości aplikacji do inicjowania obsługi administracyjnej skopiuj wartość guid skojarzoną z *polem Identyfikator obiektu.* Ta wartość jest również nazywana **ServicePrincipalId** aplikacji i będzie używana w operacjach Eksploratora wykresu firmy Microsoft.

   ![Identyfikator jednostki usługi aplikacji workday](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Krok 2: Zaloguj się do Eksploratora wykresów firmy Microsoft

1. Uruchamianie [Eksploratora wykresów firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer)
1. Kliknij przycisk "Zaloguj się za pomocą firmy Microsoft" i zaloguj się przy użyciu poświadczeń administratora globalnego usługi Azure AD lub administratora aplikacji.

    ![Logowanie do programu Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Po pomyślnym zalogowaniu zobaczysz szczegóły konta użytkownika w lewym okienku.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Krok 3: Pobieranie identyfikatora zadania inicjowania obsługi administracyjnej aplikacji inicjowania obsługi administracyjnej

W Eksploratorze wykresu firmy Microsoft uruchom następującą kwerendę GET zastępującą [servicePrincipalId] **z servicePrincipalId** wyodrębniony z [kroku 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Otrzymasz odpowiedź, jak pokazano poniżej. Skopiuj "atrybut id" obecny w odpowiedzi. Ta wartość jest **ProvisioningJobId** i będzie używany do pobierania podstawowych metadanych schematu.

   [![Identyfikator zadania inicjowania obsługi administracyjnej](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Krok 4: Pobierz schemat inicjowania obsługi administracyjnej

W Eksploratorze wykresu firmy Microsoft uruchom następującą kwerendę GET, zastępując [servicePrincipalId] i [ProvisioningJobId] z ServicePrincipalId i ProvisioningJobId pobrane w poprzednich krokach.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Skopiuj obiekt JSON z odpowiedzi i zapisz go w pliku, aby utworzyć kopię zapasową schematu.

### <a name="step-5-import-the-provisioning-schema"></a>Krok 5: Importowanie schematu inicjowania obsługi administracyjnej

> [!CAUTION]
> Ten krok należy wykonać tylko wtedy, gdy trzeba zmodyfikować schemat dla konfiguracji, której nie można zmienić za pomocą witryny Azure Portal lub jeśli trzeba przywrócić konfigurację z wcześniej kopii zapasowej pliku z prawidłowym i działającym schematem.

W Eksploratorze wykresu firmy Microsoft skonfiguruj następującą kwerendę PUT, zastępując [servicePrincipalId] i [ProvisioningJobId] z ServicePrincipalId i ProvisioningJobId pobrane w poprzednich krokach.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na karcie "Treść żądania" skopiuj zawartość pliku schematu JSON.

   [![Treść żądania](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Na karcie "Nagłówki żądań" dodaj atrybut nagłówka typu zawartości o wartości "application/json"

   [![Nagłówki żądań](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Kliknij przycisk "Uruchom kwerendę", aby zaimportować nowy schemat.
