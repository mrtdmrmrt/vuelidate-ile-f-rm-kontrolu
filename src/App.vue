<template>
  <div class="container">
    <h3 class="text-center mt-3">Vuelidate ile Form Kontrolü</h3>
    <div class="d-flex justify-content-center align-content-center flex-row">
      <div class="card p-4 mt-3 mr-4  shadow">
        <form style="width: 350px" @submit.prevent="onSubmit">
          <div class="form-group">
            <label>E-posta Adresiniz</label>
            <!--$v dediğimiz aslında vuenun bizim için validatei çağırır bir nevi validatein kısaltması gibi $touch ise validate kuralıdır-->
            <!--@input yerine @blur yaptık @blur bize alanın dışına tıkladığımız da uyarıyı gösterir.-->
            <!--@blur kullanmıycaksak aynı görevi v-model="$v.email.$model"-->
            <input 

            @blur="$v.email.$touch()"
            v-model="email" 
            type="email" 
            class="form-control"
            :class="{'is-invalid' : $v.email.$error}" 
            placeholder="E-posta adresini giriniz">
            <small v-if="!$v.email.required" class="form-text text-danger">*Bu alan zorunludur</small>
            <small v-if="!$v.email.email" class="form-text text-danger">*Lütfen Geçerli Bir Eposta Yazınız</small>
          </div>
          <div class="form-group">
            <label>Şifre</label>
            <input v-model="$v.password.$model" type="password" class="form-control" placeholder="Şifrenizi giriniz">
            <small v-if="!$v.password.required" class="form-text text-danger">*Bu alan zorunludur</small>
            <small v-if="!$v.password.numeric" class="form-text text-danger">*Lütfen Şifrenizi Sadece Rakamlardan Oluşturununuz.</small>
            <!--$v.password.$params.maxLength.max} dediğimiz aşağıda bizim parametre gönderdiğimiz için bu şekildede kullanabiliriz-->
            <small v-if="!$v.password.minLength" class="form-text text-danger">*Lütfen Şifrenizi En Az 6 Karakterden Oluşturun.</small>
            <small v-if="!$v.password.maxLength" class="form-text text-danger">*Lütfen Şifrenizi En Fazla {{$v.password.$params.maxLength.max}} Karakterden Oluşturun.</small>
          </div>
          <div class="form-group">
            <label>Şifre Tekrar</label>
            <input v-model="$v.repassword.$model" type="password" class="form-control" placeholder="Şifrenizi tekrar giriniz">
             <small v-if="!$v.repassword.required" class="form-text text-danger">*Bu alan zorunludur</small>
            <small v-if="!$v.repassword.numeric" class="form-text text-danger">*Lütfen Şifrenizi Sadece Rakamlardan Oluşturununuz.</small>
            <!--$v.password.$params.maxLength.max} dediğimiz aşağıda bizim parametre gönderdiğimiz için bu şekildede kullanabiliriz-->
            <small v-if="!$v.repassword.minLength" class="form-text text-danger">*Lütfen Şifrenizi En Az 6 Karakterden Oluşturun.</small>
            <small v-if="!$v.repassword.maxLength" class="form-text text-danger">*Lütfen Şifrenizi En Fazla {{$v.repassword.$params.maxLength.max}} Karakterden Oluşturun.</small>
            <small v-if="!$v.repassword.sameAs" class="form-text text-danger">*Girdiğiniz şifreler aynı değildir..</small>
          </div>
          <div class="form-group">
            <label>Yaşınız</label>
            <input v-model="$v.age.$model" type="text" class="form-control" placeholder="Yaşınızı Giriniz..">
             <small v-if="!$v.age.required" class="form-text text-danger">*Bu alan zorunludur</small>
             <small v-if="!$v.age.numeric" class="form-text text-danger">*Yaş kısmına sadece rakam girilmelidir</small>
             <small v-if="!$v.age.between" class="form-text text-danger">
              *Kayıt Olabilmeniz İçin Yaşınız <strong>{{$v.age.$params.between.min}} ile {{$v.age.$params.between.max}}</strong> Arasında Olmalıdır</small>
           
           
          </div>
          <div class="form-group">
            <label>Kayıt olmak istediğiniz kategori</label>
            <select v-model="$v.selectedCategory.$model" class="form-control">
              <option v-for="category in categories" :value="category">{{ category }}</option>
            </select>
            <small v-if="!$v.selectedCategory.checked" class="form-text text-danger">*Sadece yazılım Kategorisine aitkayıt oluşturabilirsiniz.</small>
          </div>

          <a @click="newHobby" class="text-white btn btn-secondary rounded-0 btn-sm">İlgi Alanı Ekle</a>
          <small v-if="!$v.hobbies.required" class="form-text text-danger">*Bu alan zorunludur</small>
          <small v-if="!$v.hobbies.minLength" class="form-text text-danger">*İlhi Alanlarınız En Az {{$v.hobbies.$params.minLength.min}} Tane Olmalıdır.</small>

          <ul class="list-group mt-3 mb-3 border-0">
            <li v-for="(hobby,index) in hobbies" class="list-group-item d-flex pl-2">
              <input 
              type="text" 
              class="form-control mr-2" 
              @blur="$v.hobbies.$each[index].value.$touch()"
              :class="{'is-invalid' : $v.hobbies.$each[index].$error }"
              v-model="hobby.value">
              <button class="btn btn-sm btn-danger rounded-0" @click="hobbies.splice(index, 1)">Sil</button>
            </li>
          </ul>
         <!--$v.$invalid sayesinde otomatik kontrol sağlanır-->
          <button class="btn btn-outline-secondary rounded-0" :disabled="$v.$invalid">Kaydet</button>
        </form>
      </div>
      <div class="card p-4 mt-3 shadow" style="width: 400px">
        <p>{{$v.repassword}}</p>
      </div>
    </div>
  </div>
</template>
<script>
  //sameAs aslında (şununla aynıysa) anlamında kullanılılır
  import {required,email,numeric,minLength,maxLength,sameAs, between} from "vuelidate/lib/validators"
  export default {
    data() {
      return {
        email : null,
        password : null,
        repassword : null,
        selectedCategory : "Yazılım",
        age : null,
        categories : ["Yazılım", "Donanım", "Cloud", "Sunucular", "Unix", "Linux", "Mac OS", "Windows"],
        hobbies: []
      }
    },
    validations : {
      email : {
        required,
        email,
        //kendimize ait validate
        //return value !== 'mert@mert.de' ? true : false
        /*
        uniq : value=>{
          return value !== 'mert@mert.de'
        }
        */
        //setTimeout kullandık database geç cevap vermiş havası yaratmak için
        uniq : value => {
          return new Promise((resolve,reject) =>{
            setTimeout(()=>{
              resolve(value !== 'mert@mert.de')
            },1000)
            
          })
          
        }



      },
      //minLength : minlength yapmamızın sebebi min ve max karakteri belirttiğimiz içindir.ES6
      password : {
        required,
        numeric,
        minLength : minLength(6),
        maxLength : maxLength(8)
      },
      repassword : {
        required,
        numeric,
        minLength : minLength(6),
        maxLength : maxLength(8),
        sameAs : sameAs('password')
       //vm2i this kullanamadığımıziçin kullandık burda sameAs in başka kullanımı da bu şekildedir.
       /*
        sameAs: sameAs (vm=>{
          return vm.password + "87"
        })
        */
      },
      //between : between(18,60) min 18 yaşında max 60 yaşında
      age : {
        required,
        numeric,
        between : between(18,60)
      },
      selectedCategory : {
        //checked burda validater dır Kendimiz oluşturduk
        checked(val,vm){
          return vm.selectedCategory === "Yazılım" ? true : false
        }
      },
      //Bir diziye minLength uygulandığında içindeki indis sayısına bakar
      //$each bir arrayın içindeki elemanları kontrol eder ve valudate özeldir
      hobbies : {
        required,
        minLength : minLength(2),
        $each:{
          value : {
            required,
            minLength : minLength(6)
          }
        }
      }
    },
    methods: {
      onSubmit(){
        let form = {
          email : this.email,
          password : this.password,
          category : this.selectedCategory,
          hobbies : this.hobbies,
        }
        console.log(form)
      },
      newHobby(){
        let hobby = {
          id: Math.random() * Math.random() * 1000,
          value: ''
        }
        this.hobbies.push(hobby)
      }
    }
  }
</script>
