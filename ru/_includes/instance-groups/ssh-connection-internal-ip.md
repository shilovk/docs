К каждой ВМ группы будет привязан [публичный IP-адрес](../../vpc/concepts/address.md#public-addresses). Это сделано для удобства [подключения по SSH](../../compute/operations/vm-connect/ssh.md#vm-connect) к ВМ группы при проверке результата.

Если вы создадите группу ВМ без публичных IP-адресов, вы по-прежнему сможете подключаться по SSH к ВМ группы, указывая [внутренний IP-адрес](../../vpc/concepts/address.md#internal-addresses) или [FQDN](../../vpc/concepts/address.md#fqdn) виртуальной машины вместо публичного IP-адреса. Но такое подключение можно выполнить только с другой виртуальной машины, имеющей публичный IP-адрес и расположенной в той же [облачной сети](../../vpc/concepts/network.md#network) {{ yandex-cloud }}, что и ВМ группы.