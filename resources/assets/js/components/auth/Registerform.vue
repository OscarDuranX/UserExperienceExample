<template>
    <form method="post" @submit.prevent="submit" @keydown="form.errors.clear($event.target.name)">
        <div class="form-group has-feedback has-error" :class="{ 'has-error' : form.errors.has('name') }">
            <input type="text" class="form-control" placeholder="Your name here" name="name" value="" v-model="form.name"/>
            <span class="glyphicon glyphicon-user form-control-feedback"></span>
            <span class="help-bñock" v-if="form.errors.has('name')" v-text="errors.get('name')"></span>
        </div>

        <div class="form-group has-feedback" :class="{ 'has-error' : form.errors.has('email') }">
            <input type="email" class="form-control" placeholder="Your email here" name="email" value="" v-model="form.email"/>
            <span class="glyphicon glyphicon-envelope form-control-feedback"></span>
            <span class="help-block" v-if="form.errors.has('email')" v-text="form.errors.get('email')"></span>
        </div>
        <div class="form-group has-feedback" :class="{ 'has-error' : form.errors.has('password') }">
            <input type="password" class="form-control" placeholder="Your password here" name="password" v-model="form.password"/>
            <span class="glyphicon glyphicon-lock form-control-feedback"></span>
            <span class="help-block" v-if="form.errors.has('password')" v-text="form.errors.get('password')"></span>
        </div>
        <div class="form-group has-feedback">
            <input type="password" class="form-control" placeholder="Repeat your password here" name="password_confirmation" v-model="form.password_confirmation"/>
            <span class="glyphicon glyphicon-log-in form-control-feedback"></span>
        </div>
        <div class="row">
            <div class="col-xs-1">
                <label>
                    <div class="checkbox_register icheck">
                        <label>
                            <input type="checkbox" name="terms" v-model="form.terms">
                        </label>
                    </div>
                </label>
            </div><!-- /.col -->
            <div class="col-xs-6">
                <div class="form-group">
                    <button type="button" class="btn btn-block btn-flat" data-toggle="modal" data-target="#termsModal">Termsbutton</button>
                </div>
            </div><!-- /.col -->
            <div class="col-xs-4 col-xs-push-1">
                <button type="submit" class="btn btn-primary btn-block btn-flat" :disabled="form.errors.any()">
                    <span v-show="!form.submitting">Register</span>
                    <i class="fa fa-spinner fa-spin" v-show="form.submitting"></i>
                </button>
            </div><!-- /.col -->
        </div>
    </form>
</template>
<script>
class Form{
  /**
  * Constructor
  */
  constructor (originalFields) {
    this.submitting = false
    this.fields = originalFields

    for (let field in originalFields) {
      this[field] = originalFields[field]
    }

    this.errors = new Errors()
  }
  /**
  * Reset properties
  */
  reset() {
    this.submitting = false

    this.fields= {}

    for (let field in originalFields) {
      this[field] = ''
    }

    this.errors.clear()
  }

  data() {
    let data = {}

    for(let field in this.fields){
      data[field] = this[field]
    }

    return data
  }

  patch(url) {
    return this.submit('patch', url);
  }

  delete(url) {
    return this.submit('delete', url);
  }

  post(url) {
    return this.submit('post', url);
  }
  put(url) {
    return this.submit('put', url);
  }

  submit(requestType, url) {
    this.submitting = true
    return new Promise( (resolve, reject) => {
      axios[requestType](url,this.data())
       .then(response => {
         console.log("response")
         this.submitting = false
         this. onSuccess(response)
         resolve(response)
       })
       .catch(error => {
         console.log("error")
         this.submitting = false
         this.onFail(error)
         reject(error)
       })
    });

  }

  onSuccess(data){
    //TODO
    this.reset()
  }
  onFail(data){
    //TODO
    this.errors.record(error.response.data)
  }
}
class Errors {
    /**
    * Constructor
    */
    constructor(){
        this.errors = {}
    }
    any() {
        return Object.keys(this.errors).length > 0
    }
    //API
    has(field){
      return this.errors.hasOwnProperty(field)
    }
    /**
    * Retrieve the error message for a field
    * @param field
    * @returns {*}
    */
    get(field){
      if (this.errors[field]){
        return this.errors[field][0]
      }
    }

    /**
     * Retrieve all errors messages for a field
     * @param field
     * @returns {*}
     */
    getAll(field) {
        if (this.errors[field]) {
            return this.errors[field]
        }
    }

    record(errors){
      this.errors = errors
    }

    clear(field){
      console.log(field)
      if (field) {
        delete this.errors[field];
        return;
      }
      this.errors = {}
    }
}

export default {
  mounted() {
    console.log('Component Register Form mounted.')
  },
  data: function (){
    return {
      form: new Form({ name: '', email: '',  password: '', password_confirmation: '',  terms: true }),
    }
  },
  methods: {
    submit() {
      this.form.post('/register')
      .them(response => {
        console.log(response)
        window.location.replace('/home')
      }).catch( errro => {
        console.log(error.response.data)
      })
    }
  }
}
</script>
