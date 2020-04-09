---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986792"
---
## <a name="create-a-ruby-application"></a>Tworzenie aplikacji Ruby
Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji Ruby na platformie Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurowanie aplikacji do korzystania z usługi Service Bus
Aby korzystać z usługi Service Bus, pobierz i użyj pakietu Ruby platformy Azure, który zawiera zestaw bibliotek wygody, które komunikują się z usługami REST magazynu.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "gem install azure" w oknie poleceń, aby zainstalować klejnot i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Korzystając z ulubionego edytora tekstu, dodaj następujące elementy do górnej części pliku Ruby, w którym zamierzasz korzystać z pamięci masowej:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Konfigurowanie połączenia usługi Service Bus
Użyj następującego kodu, aby ustawić wartości obszaru nazw, nazwy klucza, klucza, sygnatariusza i hosta:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ustaw wartość obszaru nazw na wartość utworzoną, a nie cały adres URL. Na przykład użyj **"yourexamplenamespace",** a nie "yourexamplenamespace.servicebus.windows.net".

Podczas pracy z wieloma obszarami nazw można przekazać klucz i jego `SharedAccessSigner` nazwę konstruktorowi podczas tworzenia obiektów

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
