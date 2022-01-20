---
title: "Giys Projesi"
date: 2022-01-19T10:50:26+03:00
---


Kişisel bir bilgisayarda güvenlik ayarları, kurulan yazılımlar, parola yönetimi, dosyaların yedeklenmesi gibi her türlü düzenleme, ayar ve kullanım durumları son kullanıcı tarafından belirlenebilir ve uygulanabilir. Ancak bir bilgisayar kurum içinde istemci olarak konumlandırıldığında başta
güvenlik olmak üzere, kaynak yönetimi, verimlilik gibi parametreler ön planda olduğundan yönetim ve kontrol son kullanıcılara bırakılmamalıdır.
İşletim sistemleri kamu ve özel kuruluşlarda kullanımının önündeki ciddi engellerden biri bu işletim sisteminin kurulu olduğu istemci makineleri yöneten ve kontrol eden
etkin bir sistemin mevcut olmamasıdır. Projemiz sahip olduğu güvenlik özellikleri ile sadece ulusal değil küresel anlamda da pek çok yenilikçi
yönleri olan bir İstemci Yönetim ve Kontrol Sisteminin geliştirilmesi hedeflenmiştir.

**Projenin Amacı**

Projemizin amacı özellikle kamu kurumları başta olmak üzere büyük ölçekli kurum ve kuruluşlarda işletim sistemlerinin açık kaynak işletim sistemlerine göç
engellerini kaldırmaktır. İşletim sistemi göçü sonrası Pardus'ta çalışmaya başlayan kurum ve kuruluşlar hem Pardus'ta karşılaştıkları kullanım
problemlerine anında ve uzaktan müdahale edebilmeyi hem de daha önceki sistemlerde sahip oldukları ağ yönetimi, kullanıcı yönetimi, kullanım
kontrolleri ve dosya paylaşımı gibi özellikleri etkin bir biçimde kullanmaya devam etmeyi beklemektedir. 

Projemiz;

* uzaktan kontrol ve yönetim sağlayan eklentileri ve
* dosya paylaşımı,
* envanter takip ve yönetimi 
sağlayacak yetkinlikleri ile kurumların Pardus göçünü kolaylaştırıp teşvik ederek
yerli işletim sistemimizin yaygınlaştırılmasını ve milli ekonomiye büyük bir katma değer oluşturulmasını amaçlamaktadır. Projemizin yeteneklerinin
yanı sıra kurumlardaki işlemlerin güvenli olarak yapılması için gerekli teknolojiler ve protokoller geliştirilmiştir.
{{<mermaid align="left">}}
sequenceDiagram
    actor User
    participant WebUI
    participant TaskManager
    participant AgentManager
    participant MessageManager
    participant RabbitMQ

    User ->> WebUI: get_task_list()
    WebUI ->> TaskManager : get_task_list()
    TaskManager -->> WebUI: task_list
    User ->> WebUI: get_agent_list()
    WebUI ->> AgentManager : get_agent_list()
    AgentManager -->> WebUI : agent_list

    par 
        User->>WebUI: select_task()
    and 
        User->>WebUI: select_agent()
    and
        User->>WebUI: select_task_type()
    end
    
    WebUI ->> TaskManager : load_task(agent, task)
    TaskManager ->> TaskManager: save_task(agent, task)
    TaskManager ->> AgentManager: send_task_to_agent(agent, task)
    AgentManager ->> MessageManager: send_task_message_to_message_broker(agent, task)
    MessageManager ->> MessageManager : get_agent_queue(agent)
    MessageManager ->> RabbitMQ :send_task_message_to_agent_queue(qgent_queue, task_message)
    RabbitMQ -->> MessageManager : message_sent_result
    MessageManager ->> MessageManager: create_event() listen_message_result_event
    MessageManager -->> AgentManager :task_sent_result
    AgentManager -->> TaskManager :task_sent_result
    TaskManager ->> TaskManager :listen_task_result_event 
    TaskManager ->> WebUI: task_result

{{< /mermaid >}}
<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>

**Yenilikçi Yönleri**

* Sistemimizde geliştirilen ve benzer kontrol ve yönetim sistemleri ile kıyaslandığında yenilikçi olan özellikler şunlardır:
* İstemci envanterinin doğru bir şekilde tutulmasını sağlayan istemci tekil kimlik numarası parmak izi teknikleri kullanılarak elde edilmiştir.
* İstemci bilgisayarlara yüklenen ajan sistem ile ürünün yazılım ve konfigürasyon dosyalarının bütünlüğü korunmaktadır.
* Çok kullanıcılı ve ölçeklenebilir yapıda dağıtık bir mimaride dosya paylaşım sistemi oluşturulmuştur.
* Yedekleme karar destek mekanizması geliştirilmiştir.
* İstemciler ve kontrol sistemi arasındaki iletişim güvenli, güvenilir ve hızlı bir şekilde sağlanmaktadır.

***Projenin Temel Bileşenleri;***

- Merkezi LDAP kimlik doğrulama
- Paket Yönetimi
- Güvenli ve takip edilebilir dosya paylaşımı
- Yedekleme Karar Destek Mekanizması
- Windows İşletim sistemi ile uyumu
- Hata Takip modülü
- Kimlik doğrulamada mobil uygulama kullanımı

![Kullanıcı Ayarları](/giys/server/OTP.gif#center-picture)

- Sisteme ilk kaydın ajan üzerinden otomatik gerçekleşmesi
- Güvenli mesajlaşma sistemi
- Makine tekil kimlik bilgisi
- Web Arayüzü
- Makine ve ajan yazılım bütünlüğünü koruma
- Sistem izleme


{{<mermaid align="left">}}


sequenceDiagram
    participant GIYSMessageListener
    participant MessageEventListener
    participant MessageParser
    participant MessageService
    participant MessageManager
    participant RabbitMQ

    GIYSMessageListener ->> MessageEventListener : listen_message_queue()
    MessageEventListener ->> MessageParser : parse_message(message)
    MessageParser ->> MessageService : call_task(task)
    MessageService -->> MessageParser: task_result
    MessageParser ->> MessageManager: send_return_message(task)
    MessageManager -->> MessageParser: task_result_sent
    MessageManager ->>  RabbitMQ : publish_message(message)
    RabbitMQ -->>  MessageManager : message_sent_result

{{< /mermaid >}}


 <script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>