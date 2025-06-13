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

### İnit Container

          apiVersion: v1
          kind: Pod
          metadata:
            name: initcontainerpod
          spec:
            containers:
            - name: appcontainer
              image: busybox
              command: ['sh', '-c', 'echo The app is running! && sleep 3600']
          initContainers:
          - name: initcontainer
            image: busybox
            command: ['sh', '-c', "until nslookup myservice.default.svc.cluster.local; do echo waiting for nslookup myservice.default.svc.cluster.local; sleep 2; done"]

- İnit containerlar bizim ana containerımız başlatılmadan önce yapılması gerekenleri yapan bir container
- İnit container tamamlanmadan ana container başlatılmaz

### LABEL
`kubectl get pods -l "app" --show-labels`
- Bu komut sayesinde podları listelerken app anahtarına sahip bütün podları listeler

`kubectl get pods -l "app=firstapp" --show-labels`
- Aralığı daha da daraltmak için değerini de verirsek aralığımız küçülür.

`kubectl get pods -l "app=firstapp,tier=frontend" --show-labels`
- "," koyarak ekstra olarak aralığımızı daraltabiliriz. "," ve anlamına gelmektedir.

`kubectl get pods -l "app=firstapp,tier!=frontend" --show-labels`
- "!" ile yazdığımız kriterin dışında olanları listeler.

**Farklı Bir Kullanım Olarak**

`kubectl get pods -l 'app in (firstapp)' --show-labels` 

- Bu komut sayesinde podları listelerken app anahtarına sahip bütün podları listeler

`kubectl get pods -l 'app in (firstapp,secondapp)' --show-labels`

- Burda ise app i firstapp ve secondapp değerlerine sahip olanları listeler

`kubectl get pods -l 'app notin (firstapp)' --show-labels`

- Bu komutta yazdığımızın dışındakileri listelemesini istiyoruz

`kubectl get pods -l 'app,app notin (firstapp)' --show-labels`

- Burda ise app anahtarı olanların içinde firstapp olmayanları listeliyoruz.

`kubectl get pods -l 'app,app notin (firstapp), tier in (frontend)' --show-labels`

`kubectl label pods pod9 app=thirdapp`

- Burda app anahtarının altında thirdapp değerinde yeni bir label eklemesini yapıyoruz.
- Bu labeli daha sonradan kaldırmak için ise

`kubectl label pods pod9 app-`

`kubectl label --overwrite pods pod9 team=team3`
- Bu kodla ise güncelleme yapabiliyoruz.

`kubectl label pods --all foo=bar`
- Bütün podlara label ekleme komutu.

### Annotations
          annotations:
              owner: "Gokberk Gokalan"
              notification-email: "admin@k8sfundamentals.com"
              releasedate: "01.01.2021"
              nginx.ingress.kubernetes.io/force-ssl-redirect: "true"

- Label olarak eklememizin sakıncalı olacağı bilgileri açıklama "annotation" olarak ekleriz

`kubectl apply -f podannotation.yaml`

`kubectl describe pods annotationpod`

Annotations:      nginx.ingress.kubernetes.io/force-ssl-redirect: true
                  notification-email: admin@k8sfundamentals.com
                  owner: Gokberk Gokalan
                  releasedate: 01.01.2021
  
- Bir nevi açıklamadır.

`kubectl annotate pods annotationpod foo=bar`

- Bu komut ile annotation ekleme yapabiliriz.

`kubectl annotate pods annotationpod foo-`

- Bu kodla da eklediğimiz annotation u kaldırabilriz.

### Namespace
`kubectl get pods --namespace kube-system`

- Sadece istediğimiz namesoace i listeler

`kubectl get pods -A`

- Bütün namespaceleri listeler.
  
          apiVersion: v1
          kind: Namespace
          metadata:
            name: development
          ---
          apiVersion: v1
          kind: Pod
          metadata:
            namespace: development
            name: namespacepod
          spec:
            containers:
            - name: namespacecontainer
              image: nginx:latest
              ports:
              - containerPort: 80
  
- Namespacelari yaml olarakta tanımlayabilriz.

`kubectl get pods -n development`

- Tanımlı oldupu namespace ile çağırılabilir.
 
- Sadece belli bir namespace üzerinde çalışacaksak Aşağıdaki kod ile varsayılan namespace i değiştirebiliriz.

`kubectl config set-context --current --namespace=development`

- Namespace silmek için ise;
  
`kubectl delete namespaces development`

### Deployment
- Deployment'lar sürekli güncelleme yapacağım aynı türden containerların oluşmasını sağlayan bir komut.

`kubectl create deployment firstdeployment --image=nginx:latest --replicas=2`

- Bu komut ile isim, imaj ve kaç tane olacağını girerek deployment oluşturabiliriz.

`kubectl delete pods firstdeployment-5b665bb68f-nbsqz`

- Bu komut ile sildiğimizde ise 1 tanesini sildiğimiz ve geriye 1 tane kaldığı için isteklerimizle uyuşmadığından yeniden 1 tane daha pod oluşturur

`kubectl set image deployment/firstdeployment nginx=httpd`

- İlk ayarladığımız imajı bu şekilde değiştirebiliriz.

`kubectl scale deployment firstdeployment --replicas=5`

- Bu komut ile kaç tane oluşturacağımızı değiştirebiliriz (arttırıp, azaltılabilir)

` kubectl delete deployments firstdeployment`

- Bu komut ile de oluşturduğumuz deployment ı kaldırabiliriz.

**Bu şekilde imperative şekilde oluşturup düzenlemeyi gördük.**

**Sırada ise Declarative şeklinde oluşturmaya bakalım**

          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: firstdeployment
            labels:
              team: development
          spec:
            replicas: 3
            selector:
              matchLabels:
                app: frontend
            template:
              metadata:
                name: examplepod
                labels:
                  app: frontend
              spec:
                containers:
                - name: nginx
                  image: nginx:latest
                  ports:
                  - containerPort: 80

`kubectl rollout undo deployment firstdeployment`

- Bu kod yaptığımız değişiklikleri geri almamızı sağlar.
- Replicaset sayesinde bu işlemi yapabiliriz.
- Replicaset objesi oluşturmak yerine deployment kullanırız bunlar beraber çalışmaktadır.

### Rollout ve Rollback

`kubectl edit deployment rolldeployment --record`

- Değiklik yapmamızı sağlar ve bu değişikliği record komutu ile kaydederiz.

`kubectl set image deployment rolldeployment nginx=httpd:alpine --record=true`

- Burda set komutu ile güncelleme yapıyoruz set ediyoruz.

`kubectl rollout history deployment rolldeployment`

- Bu komutta yaptığımız değişikliklerin tarihçesini revisionlarını bize listeler.

`kubectl rollout undo deployment rolldeployment --to-revision=3`

- Bu komut ile istediğimiz revision u tekrardan geri alabilme şansımız olmakta.





 
  
  
            
          
          
          
          
          
          
          
          
            
















