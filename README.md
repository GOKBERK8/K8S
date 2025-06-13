# K8S
## Kendim için k8s özellikleri ve kullanımı

## İçindekiler
- [Kubectl Config](#kubectl-config)
- [Kubectl Kullanımı](#kubectl-kullanımı)
## Kubectl Config <a name="my-custom-anchor-point"></a>

- `kubectl config get-contexts` // mevcut contextleri listeler
- `kubectl config current-context` // kullanılan contexti söyler
- `kubectl config use-context "minikube"` // context değiştirmek için kullanılan komut burda ise minikube contextini varsayılan olarak ayarla diyoruz
- `kubectl get nodes` // varsayılan context i getirir, bilgilerini gösterir
fghdfghfg

fgdhfghfg

hfgdhfgh

fgdhfgdh

fgdhfgd

hfdghfgd

hfdghfgd


hfdghfdg

hfdgh

fgdhdfgh

gfdhfgdh

fdghfghd

fdghfg

dhfdgh

### Kubectl Kullanımı <a name="my-custom-anchor-point1"></a>
- `kubectl cluster-info` // kubernetes cluster ile ilgili temel bir bilgi edinmek için kullandığımız komut
- `kubectl` // bu komut ile kullanabileceğimiz komutları bize listeler
- `kubectl .... --help` // "...." ile belirttiğim yere herhangi bir komut yazıp yanına --help eklediğim zamanda bu kullandığım komutun nasıl kullanıldığı ve ne işe yaradığı hakkında bilgileri elde edebilirim
- `kubectl "fiil, yapmak istediğimiz işlem" "obje, tip, tür" "gerekirse obje tipinin ismi"`
- `kubectl get|delete|edit|apply pods testpods` // aşağı yukarı böyle gözükmesi gerekiyor
- `kubectl get pods` // sistem varsayılan namespace i ney ise ordaki podları listeler
- **pod** : bir veya birden fazla container içeren kubernetes altındaki standart dapıtılabilir birimdir
- `kubectl get pods -n kube-system` // -n ile namespace i değiştirebiliriz bu örnekte kube-system altındaki podları listeledik
- `kubectl get pods -A` // namespace göz etmeksizin bütün podları listeler
- `kubectl get pods -A -o wide` // -o ile output yani biz komutu girdiğimizde bize çıktı olarak ne vermesini istediğimizi yazabiliyoruz bu örnekte genişletilmiş çıktı istedik
- `kubectl get pods -A -o yaml` // yaml formatında gösterir
- `kubectl get pods -A -o json` // json formatında gösterir
- `-o json|jsonpath|go-template|yaml|wide|custom-columns` // gibi formatları mevcut
- `kubectl explain pod` // explain komutu objeler hakkında bilgi verir bu komutta podlar hakkında bilgileri bize getirir
