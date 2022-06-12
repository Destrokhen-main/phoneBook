<template>
  <div class="window" @click="closeModal()">
    <div class="modal" @click.stop="">
      <div class="modal__header">
        <div class="modal__header__title">
          <span>Добавить пользователя</span>
        </div>
        <div class="modal__header__exit">
          <div class="exit-button" @click="closeModal">
            &#215;
          </div>
        </div>
      </div>
      <div class="modal__body">
        <div class="row">
          <div class="modal__body_col-1">Имя</div>
          <div class="modal__body_col-2">
            <input class="form" type="text" v-model="name" />
          </div>
        </div>
        <div class="row">
          <div class="modal__body_col-1">Телефон</div>
          <div class="modal__body_col-2">
            <input class="form" type="text" v-mask="'+7 (###) ###-##-##'" v-model="phone" />
          </div>
        </div>
        <div class="row">
          <div class="modal__body_col-1">Начальник</div>
          <div class="modal__body_col-2">
            <select class="form" v-model="parent" type="select">
              <option value="-1" selected>--Выбрать--</option>
              <option
                v-for="parent in parents"
                :key="parent.id"
                :value="parent.id"
              >
                {{parent.name}}
              </option>
            </select>
          </div>
        </div>
        <div class="modal__body_col-full">
          <button class="button-primary" @click="createNewUser">
            Сохранить
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ModalWindow',
  emits: ['close', 'create'],
  props: {
    parents: {
      type: Array,
      required: true
    }
  },
  data () {
    return {
      name: '',
      phone: '',
      parent: '-1'
    }
  },
  methods: {
    closeModal () {
      this.$emit('close')
    },
    createNewUser () {
      const reg = /[^0-9]/ig
      const phone = this.phone.replaceAll(reg, '')

      const validator = (value, type) => {
        switch (type) {
          case 'text':
            if (value !== '' || value !== ' ') {
              return true
            } else {
              return false
            }
          case 'phone':
            if (value.length === 11) {
              return true
            } else {
              return false
            }
        }
      }

      if (
        validator(this.name, 'text') &&
        validator(phone, 'phone')
      ) {
        this.$emit('create',
          {
            name: this.name,
            phoneFormat: this.phone,
            phoneNoFormat: phone,
            parent: this.parent
          }
        )
      } else {
        alert('Некоторые поля не заполнены верно')
      }
    }
  }
}
</script>

<style>

.window {
  background-color:rgba(202, 202, 202, 0.407);
  display: flex;
  justify-content: center;
  align-items: center;
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  z-index: 10;
}

.modal {
  z-index: 100;
  width: 500px;
  background-color: white;
  border-radius: 10px;
  padding: 20px;
}

.modal__header {
  display:flex;
  justify-content: space-between;
  align-items: center;
}

.exit-button {
  padding: 5px 10px;
  border-radius: 10px;
  border:1px solid grey;
  cursor: pointer;
  transition:1s;
}

.exit-button:hover {
  background-color: rgb(214, 211, 211);
}

.modal__body {
  margin-top: 30px;
}

.modal__body_col-1 {
  width: 30%;
}

.modal__body_col-2 {
  width: 70%;
  display: flex;
}

.row {
  display: flex;
  align-items: center;
  width: 100%;
  margin-bottom: 10px;
  max-width: 100%;
  justify-content: space-between;
}

.form {
  width: 100%;
  padding-left: 10px;
  padding-top: 10px;
  padding-bottom: 10px;
}

.modal__body_col-full {
  width: 100%;
  margin-top: 20px;
}

select[class~=form] {
  width: 100%;
}
</style>
