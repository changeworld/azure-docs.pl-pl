---
title: Jak udostępniać usługi Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Dowiedz się, jak korzystać z usługi Azure Dev Spaces w celu udostępniania przestrzeni dewelopera w usłudze Azure Kubernetes innym osobom w zespole
keywords: 'Docker, Kubernetes, Azure, AKS, Usługa Azure Kubernetes, kontenery, Helm, siatka usług, routing siatki usług, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474411"
---
# <a name="share-azure-dev-spaces"></a>Udostępnianie obszarów Azure Dev Spaces

Dzięki usłudze Azure Dev Spaces możesz udostępniać swoje miejsce deweloperów innym osobom w zespole. Każdy deweloper może pracować we własnej przestrzeni bez obawy o złamanie innych. Ponadto praca razem w jednym miejscu można włączyć do testowania kodu end-to-end bez konieczności tworzenia makiety lub symulować zależności. Więcej informacji można znaleźć w przewodniku po [rozwoju zespołu.](../team-development-nodejs.md)

## <a name="set-up-a-dev-space-for-multiple-developers"></a>Konfigurowanie miejsca dewelopera dla wielu deweloperów

1. Tworzenie obszaru deweloperów na platformie Azure. Wybierz [opcję .NET Core i VS Code](../get-started-netcore.md), [.NET Core i Visual Studio](../get-started-netcore-visualstudio.md)lub [Node.js i VS Code](../get-started-nodejs.md). Musisz mieć dostęp właściciela lub współautora do wybranej subskrypcji platformy Azure.
1. Upewnij się, że każdy członek zespołu ma [odpowiednie uprawnienia dostępu do kontrolera azure dev spaces.](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis) Na przykład można skonfigurować **grupę zasobów** azure dev space, aby [udzielić dostępu współautora](/azure/active-directory/role-based-access-control-configure) dla każdego członka zespołu. Można sprawdzić grupę zasobów obszaru deweloperów, uruchamiając to polecenie:`azds show-context`
1. Poproś członków zespołu, aby **wybrali przestrzeń deweloperów,** aby się w niej rozwijali.
   * **Wiersz polecenia lub kod VS:** Aby wyświetlić istniejące `azds space list`usługi Azure Dev Spaces, do których masz dostęp: . Aby wybrać przestrzeń `azds space select`deweloperów: .
   * **Ide programu Visual Studio:** Otwórz projekt w programie Visual Studio, wybierz **pozycję Azure Dev Spaces** z listy rozwijanej ustawień uruchamiania. W oknie dialogowym, które zostanie otwarte, wybierz istniejący klaster.

     ![Z listy rozwijanej ustawienia uruchamiania programu Visual Studio](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [Dowiedz się więcej o rozwoju zespołu,](../team-development-nodejs.md) aby uzyskać więcej informacji.
