---
title: Role i wymagania dotyczące usługi Azure Data Share
description: Dowiedz się więcej o rolach i wymaganiach kontroli dostępu dla dostawców danych i użytkowników danych w celu udostępniania danych w udziale danych platformy Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 34c73a6bd400da076c68f308a2100a0f4569bd04
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490581"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Role i wymagania dotyczące usługi Azure Data Share 

W tym artykule opisano role wymagane do udostępniania danych za pomocą udziału danych platformy Azure, a także do akceptowania i odbierania danych przy użyciu udziału danych platformy Azure. 

## <a name="roles-and-requirements"></a>Role i wymagania

Udział danych platformy Azure używa zarządzanych tożsamości dla usług platformy Azure (wcześniej znanych jako MSIs) do uwierzytelniania na podstawowych kontach magazynu, aby można było odczytać dane, które mają być współużytkowane przez dostawcę danych, a także odbierać dane udostępnione jako odbiorca danych. W związku z tym nie istnieje wymiana poświadczeń między dostawcą danych i konsumentem danych. 

Tożsamość usługi zarządzanej należy udzielić dostępu do bazowego konta magazynu lub bazy danych SQL. Usługa Udostępnianie danych platformy Azure używa tożsamość usługi zarządzanej zasobów udziału danych platformy Azure do odczytu i zapisu danych. Użytkownik udziału danych platformy Azure musi mieć możliwość utworzenia przypisania roli dla tożsamość usługi zarządzanej do konta magazynu lub bazy danych SQL, z której korzystają dane. 

W przypadku magazynu, uprawnienia do tworzenia przypisań ról istnieją w roli **właściciela** , roli administrator dostępu użytkowników lub roli niestandardowej z przypisaną opcją Microsoft. Autoryzacja/przypisania ról/przypisane uprawnienia do zapisu. 

Jeśli nie jesteś właścicielem danego konta magazynu i nie możesz samodzielnie utworzyć przypisania roli dla zarządzanej tożsamości zasobu udziału danych platformy Azure, możesz poprosić administratora platformy Azure o utworzenie przypisania roli w Twoim imieniu. 

Poniżej znajduje się Podsumowanie ról przypisanych do tożsamości zarządzanej przez zasób udostępniania danych:

| |  |  |
|---|---|---|
|**Typ magazynu**|**Magazyn Dostawca danych**|**Magazyn docelowy odbiorcy danych**|
|Azure Blob Storage| Czytnik danych obiektów blob magazynu | Współautor danych obiektu blob magazynu
|Azure Data Lake Gen1 | Właściciel | Nieobsługiwane
|Azure Data Lake Gen2 | Czytnik danych obiektów blob magazynu | Współautor danych obiektu blob magazynu
|Azure SQL | dbo | dbo 
|

### <a name="data-providers"></a>Dostawcy danych 
Aby dodać zestaw danych do udziału danych platformy Azure, tożsamość zarządzana przy użyciu zasobów dostawcy danych musi zostać dodana do roli czytnika danych obiektu blob magazynu. Jest to wykonywane automatycznie przez usługę udziału danych platformy Azure, jeśli użytkownik dodaje zbiory informacji za pośrednictwem platformy Azure i jest właścicielem konta magazynu lub jest członkiem roli niestandardowej, która ma przypisane uprawnienie Microsoft. Autoryzacja/Przypisywanie ról/zapis. 

Użytkownik może również mieć uprawnienia administratora platformy Azure, dodając tożsamość zarządzaną zasobów udziału danych do roli czytnika danych obiektów blob magazynu ręcznie. Ręczne utworzenie tego przypisania roli przez administratora spowoduje unieważnienie właściciela konta magazynu lub przypisanie roli niestandardowej. Dotyczy to danych udostępnianych z usługi Azure Storage lub Azure Data Lake Gen2. 

W przypadku udostępniania danych z programu Azure Data Lake Gen1 należy wykonać przypisanie roli do roli właściciela. 

Aby utworzyć przypisanie roli dla tożsamości zarządzanej zasobu udział danych, wykonaj następujące czynności:

1. Przejdź do konta magazynu.
1. Wybierz pozycję **Access Control (IAM)** .
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. W obszarze *rola*wybierz pozycję *czytnik danych magazynu obiektów BLOB*.
1. W obszarze *Wybierz*wpisz nazwę konta udziału danych platformy Azure.
1. Kliknij pozycję *Zapisz*.

W przypadku źródeł opartych na języku SQL użytkownik musi zostać utworzony przez dostawcę zewnętrznego w bazie danych SQL, z którego dane są udostępniane z tą samą nazwą jak konto udziału danych platformy Azure. Przykładowy skrypt wraz z innymi wymaganiami wstępnymi dotyczącymi udostępniania opartego na języku SQL można znaleźć w samouczku [udostępnianie danych](share-your-data.md) . 

### <a name="data-consumers"></a>Odbiorcy danych
Aby można było odbierać dane, należy dodać tożsamość zarządzaną przez zasoby danych odbiorcy danych do roli współautor danych obiektów blob magazynu i/lub rolę właściciela bazy danych SQL w przypadku odbierania danych do bazy danych SQL. 

W przypadku magazynu jest to wykonywane automatycznie przez usługę udostępniania danych platformy Azure, jeśli użytkownik dodaje zestawy elementów za pośrednictwem platformy Azure i jest właścicielem konta magazynu lub jest członkiem roli niestandardowej, która ma uprawnienia Microsoft. Autoryzacja/przydziały/zapis. pisywany. 

Użytkownik może również mieć uprawnienia administratora platformy Azure, dodając tożsamość zarządzaną zasobów udziału danych do roli współautor danych obiektów blob magazynu ręcznie. Ręczne utworzenie tego przypisania roli przez administratora spowoduje unieważnienie właściciela konta magazynu lub przypisanie roli niestandardowej. Należy pamiętać, że dotyczy to danych udostępnianych w usłudze Azure Storage lub Azure Data Lake Gen2. Otrzymywanie danych do Azure Data Lake Gen1 nie jest obsługiwane. 

Aby ręcznie utworzyć przypisanie roli dla tożsamości zarządzanej zasobu udział danych, wykonaj następujące czynności:

1. Przejdź do konta magazynu.
1. Wybierz pozycję **Access Control (IAM)** .
1. Wybierz pozycję **Dodaj przypisanie roli**.
1. W obszarze *rola*wybierz opcję *współautor danych obiektów blob magazynu*. 
1. W obszarze *Wybierz*wpisz nazwę konta udziału danych platformy Azure.
1. Kliknij pozycję *Zapisz*.

Jeśli udostępniasz dane przy użyciu interfejsów API REST, musisz ręcznie utworzyć te przypisania ról, dodając konto udostępniania danych do odpowiednich ról. 

Jeśli otrzymujesz dane w źródle opartym na języku SQL, upewnij się, że nowy użytkownik został utworzony przez zewnętrznego dostawcę o takiej samej nazwie jak konto udziału danych platformy Azure. Zobacz wymagania wstępne w samouczku [akceptowanie i odbieranie danych](subscribe-to-data-share.md) . 

Aby dowiedzieć się więcej na temat dodawania przypisania roli, zapoznaj się z [tą dokumentacją,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment) która zawiera opis sposobu dodawania przypisania roli do zasobu platformy Azure. 

## <a name="resource-provider-registration"></a>Rejestracja dostawcy zasobów 

Po zaakceptowaniu zaproszenia do udziału danych platformy Azure należy ręcznie zarejestrować dostawcę zasobów Microsoft. datashare w ramach subskrypcji. Wykonaj następujące kroki, aby zarejestrować dostawcę zasobów Microsoft. datashare w ramach subskrypcji platformy Azure. 

1. W Azure Portal przejdź do **subskrypcji**.
1. Wybierz subskrypcję używaną dla udziału danych platformy Azure.
1. Kliknij pozycję **dostawcy zasobów**.
1. Wyszukaj firmę Microsoft. datashare.
1. Kliknij pozycję **zarejestruj**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o rolach na platformie Azure — [Omówienie definicji ról](../role-based-access-control/role-definitions.md)

