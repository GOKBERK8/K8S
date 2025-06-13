# K8S
## Kendim için k8s özellikleri ve kullanımı

## İçindekiler
- [Kubectl Config](#kubectl-config)
- [Kubectl Kullanımı](#kubectl-kullanımı)
- [POD](#pod)
- [İmperative Yöntem](#i̇mperative-yöntem)
- [YAML](#yaml)
- [Declarative Yöntem](#declarative-yöntem)

## Kubectl Config 
- `kubectl config get-contexts` // mevcut contextleri listeler
- `kubectl config current-context` // kullanılan contexti söyler
- `kubectl config use-context "minikube"` // context değiştirmek için kullanılan komut burda ise minikube contextini varsayılan olarak ayarla diyoruz
- `kubectl get nodes` // varsayılan context i getirir, bilgilerini gösterir

## Kubectl Kullanımı
- `kubectl cluster-info` // kubernetes cluster ile ilgili temel bir bilgi edinmek için kullandığımız komut
- `kubectl` // bu komut ile kullanabileceğimiz komutları bize listeler
- `kubectl .... --help` // "...." ile belirttiğim yere herhangi bir komut yazıp yanına --help eklediğim zamanda bu kullandığım komutun nasıl kullanıldığı ve ne işe yaradığı hakkında bilgileri elde edebilirim
- `kubectl "fiil, yapmak istediğimiz işlem" "obje, tip, tür" "gerekirse obje tipinin ismi"`
- `kubectl get|delete|edit|apply pods testpods` // aşağı yukarı böyle gözükmesi gerekiyor
- `kubectl get pods` // sistem varsayılan namespace i ney ise ordaki podları listeler
- `kubectl get pods -n kube-system` // -n ile namespace i değiştirebiliriz bu örnekte kube-system altındaki podları listeledik
- `kubectl get pods -A` // namespace göz etmeksizin bütün podları listeler
- `kubectl get pods -A -o wide` // -o ile output yani biz komutu girdiğimizde bize çıktı olarak ne vermesini istediğimizi yazabiliyoruz bu örnekte genişletilmiş çıktı istedik
- `kubectl get pods -A -o yaml` // yaml formatında gösterir
- `kubectl get pods -A -o json` // json formatında gösterir
- `-o json|jsonpath|go-template|yaml|wide|custom-columns` // gibi formatları mevcut
- `kubectl explain pod` // explain komutu objeler hakkında bilgi verir bu komutta podlar hakkında bilgileri bize getirir

## POD

          - bir veya birden fazla container içeren kubernetes altındaki standart dağıtılabilir birimdir.

          - Her podun eşsiz bir id'si bulunur.
          
          - Kuberneteste oluşturabileceğimiz en temel obje poddur.

## İmperative Yöntem
          - Bu yöntemde satır satır kod girmemiz gerekmektedir.
### İlk POD'umuzu Oluşturalım

`kubectl run firstpod --image=nginx --restart=Never`
- run komutu ile oluşturuyoruz 
- bir isim atıyoruz (firstpod)
- hangi imajdan oluşacağını belirliyoruz 
- bu pod kapandığında yeniden başlatılıp başlatılmayacağını ayarlıyoruz

#### Pod'un daha detaylı bilgilerine erişelim
`kubectl describe pods firstpod` 
- describe subcommand ile detaylı bilgiye ulaşabiliriz
- Bu komut ile Events kısmına erişebiliyoruz bu kısım bir nevi oluşturduğumuz podun tarihçesidir.

#### Pod'un loglarına erişebiliriz
`kubectl logs firstpod` 
- Bu komut ile loglarına erişebiliriz.

`kubectl logs -f firstpod`
- Bu komut ile de anlık olarak logları inceleyebiliriz.

`kubectl exec firstpod -- hostname` 
- exec komutu ile oluşturduğumuz podun içinde komut çalıştırabiliriz.
- "--" den sonra çalıştırmak istediğimiz komutu girdiğimizde bize bu komutun çıktısını verecektir.

`kubectl exec -it firstpod -- /bin/sh` 
- Oluşturduğumuz podun shelline bağlanmak için kullandığımız komut

`kubectl delete pods firstpod` 
- Bu komut ile oluşturduğumuz podu silebilriz.

# YAML

      key: value
      anahtar: değer
      rakam: 1
      anahtar1: deger1

      fizik: <br/>
        boy: 1.78 <br/>
        kilo: 65 <br/>

      kimya:
        - su
        - hava

      kimya: [su, hava, degisti]

      kimya:
        duygu:
          - ismi: Sevgi
            puan: 100
          - ismi: Nefret
            puan: 20

      run: |
        npm install
        npm run build

            // anahtar ve değerler ile oluşturuyoruz
            // "|" ile alt alta kod çalıştırabiliyoruz
            // "-" ile array oluyor
            // boşluklar sayesinde objeler oluşturabiliyoruz
          
      ---> map 
      containers:
        - name: nginx
          image: nginx:1.6
          ports:
            - containerPorts: 80
 
      ---> kubernetes
      apiVersion: apps|v1 // kubernetes üzerinde kullanacağımız objenin versionu // kubectl explain pods ile öğrenebiliriz
      kind: pod|deployment|services // hangi objeleri kullanacağımızı belirler
      metadata: // isim, label, namespace gibi bilgileri tanımladığımız yer
      spec: // uygulamanın, objenin nasıl çalışacağı hangi bilgileri içereceği bilgilerini tutar

      ---> örnek yaml dosyası

      apiVersion: v1
      kind: Pod
      metadata:
        name: app
        namespace: default
      spec:
        containers:
        - name: app
          image: nginx

      ---> ikinci bir örnek yaml dosyası
      pod1.yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: firstpod
        labels:
          app: frontend
      spec:
        containers:
        - name: nginx
          image: nginx:latest
          ports:
          - containerPort: 80
# Declarative Yöntem

        Bu yöntem sayesinde tek tek yapmamıza gerek olmadan isteklerimizi birleştirerek verebiliriz. 
        Herhangi bir değişiklikte de tekrardan bu isteklerimiz içine yazarız ve değişiklik uygulanmış olur.

`kubectl apply -f pod1.yaml` 
- Bu yaml dosyası ile bir pod oluşturmak istediğimizde kullanmamız gereken kod
- Bu kod ile yaml dosyasında yapacağımız bir değişikliği silme, tekrar oluşturma işlemlerine gerek kalmadan configure edebiliriz.

`kubectl edit pods firstpod`
- Bu komut çok kullanılmasa da bu kod sayesinde yaml dosyamızın içeriğini değiştirebiliriz
- Bu kodun çok kullanılmamasının sebebi yapılan değişikliğin yaml dosyası üzerinde gözükmemesi 

## POD Yaşam Döngüsü
- Pending
- Creating
- ImagePullBackOff
- Running
- Succeeded
- Failed
- CrashLoopBackOff

`kubectl exec -it multicontainer -c sidecarcontainer -- /bin/sh`
- Bu kod ile multicontainer içindeki bir başka container a geçebiliriz.







