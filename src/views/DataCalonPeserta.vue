<template>
  <div class="data-calon-container vector-background">
    <div class="table-container col center">
      <h3 class="header-title">Data Calon Peserta</h3>
      <div v-if="dataCalon.length !== 0">
        <table id="table-calon-peserta">
          <tr>
            <th>Nama</th>
            <th>Username</th>
            <th>Rekaman</th>
            <th>Action</th>
          </tr>
          <tr v-for="(data, index) in dataCalon" :key="index">
            <td>{{ data.display_name }}</td>
            <td>{{ data.username }}</td>
            <td>
              <vue-audio :file="data.recording" />
            </td>
            <td>
              <button class="btn" @click="toggleModal(data)">Nilai Laporan</button>
            </td>
          </tr>
        </table>
      </div>
      <div v-else>
        <b-row align-v="center">
          <p text-center>Tidak ada peserta untuk dinilai</p>
        </b-row>
      </div>
    </div>
    <div class="table-container col center">
      <h3 class="header-title">Hasil Penilaian Calon Peserta</h3>
      <div v-if="dataEvaluatedCalon.length !== 0">
        <table id="table-calon-peserta">
          <tr>
            <th>Nama</th>
            <th>Username</th>
            <th>Rekaman</th>
            <th>Action</th>
          </tr>
          <tr v-for="(data, index) in dataEvaluatedCalon" :key="index">
            <td>{{ data.display_name }}</td>
            <td>{{ data.username }}</td>
            <td>
              <vue-audio :file="data.recording" />
            </td>
            <td>
              <button class="btn" @click="toggleModalNilai(data)">Lihat Nilai</button>
            </td>
          </tr>
        </table>
      </div>
      <div v-else>
        <b-row align-v="center">
          <p text-center>Belum ada peserta yang dinilai</p>
        </b-row>
      </div>
    </div>

    <b-modal v-model="isModal" class="modal" size="sm" hide-footer centered title="Seleksi Peserta">
      <h4 class="student-name" v-if="studentModal !== null">{{studentModal.display_name}}</h4>
      <b-form @submit.stop.prevent="handleSubmit(studentModal)">
        <b-row>
          <b-col>
            <b-form-group
              id="harakatMistake-group"
              label="Kesalahan Huruf/Harakat"
              label-for="harakatMistake"
            >
              <b-form-select
                id="harakatMistake"
                v-model="form.harakatMistake"
                :options="form.scoreOption"
                required
              >
                <template v-slot:first>
                  <b-form-select-option :value="null" disabled>Pilih Opsi</b-form-select-option>
                </template>
              </b-form-select>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <b-form-group
              id="madMistake-group"
              label="Kesalahan Panjang Pendek"
              label-for="madMistake"
            >
              <b-form-select
                required
                id="madMistake"
                v-model="form.madMistake"
                :options="form.scoreOption"
              >
                <template v-slot:first>
                  <b-form-select-option :value="null" disabled>Pilih Opsi</b-form-select-option>
                </template>
              </b-form-select>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <b-form-group
              id="gunnahMistake-group"
              label="Kesalahan Gunnah"
              label-for="gunnahMistake"
            >
              <b-form-select
                required
                id="gunnahMistake"
                v-model="form.gunnahMistake"
                :options="form.scoreOption"
              >
                <template v-slot:first>
                  <b-form-select-option :value="null" disabled>Pilih Opsi</b-form-select-option>
                </template>
              </b-form-select>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <b-form-group id="isLulus-group" label="Kelulusan" label-for="isLulus">
              <b-form-select id="isLulus" v-model="form.isLulus" :options="form.graduationOption">
                <template v-slot:first>
                  <b-form-select-option :value="null" disabled>Pilih Opsi</b-form-select-option>
                </template>
              </b-form-select>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <b-form-group
              id="levelTahsin-group"
              label="Level tahsin yang disarankan"
              label-for="levelTahsin"
            >
              <b-form-select
                required
                id="levelTahsin"
                v-model="form.levelTahsin"
                :options="form.levelTahsinOption"
              >
                <template v-slot:first>
                  <b-form-select-option :value="null" disabled>Pilih Opsi</b-form-select-option>
                </template>
              </b-form-select>
            </b-form-group>
          </b-col>
        </b-row>
        <b-row>
          <b-col>
            <b-form-group id="note-group" label="Catatan" label-for="note">
              <b-form-textarea
                id="note"
                v-model="form.note"
                placeholder="Isi catatan yang perlu diketahui murid di sini..."
                rows="3"
                max-rows="4"
              ></b-form-textarea>
            </b-form-group>
          </b-col>
        </b-row>
        <div class="modal-footer">
          <b-button
            id="submit"
            type="submit"
            size="sm"
            ref="btn-submit"
            variant="none"
            class="primary mt-2 mx-auto"
          >Submit</b-button>
        </div>
      </b-form>
    </b-modal>

    <b-modal
      v-model="isModalNilai"
      class="modal"
      size="sm"
      hide-footer
      centered
      title="Seleksi Peserta"
    >
      <h4
        class="student-name"
        v-if="modalNilaiStudent !== null"
      >{{ modalNilaiStudent.display_name }}</h4>
      <b-row class="graduation">
        <b-col>
          <h3 class="primary" v-if="dataNilaiCalon.isLulus">
            <strong>Lulus</strong>
          </h3>
          <h3 class="danger" v-else>
            <strong>Tidak Lulus</strong>
          </h3>
        </b-col>
      </b-row>
      <b-row align-v="center" class="score">
        <b-col sm="12" md="4">
          <h4>{{dataNilaiCalon.harakatMistake}}</h4>
          <p>Kesalahan Harakat</p>
        </b-col>
        <b-col sm="12" md="4">
          <h4>{{dataNilaiCalon.gunnahMistake}}</h4>
          <p>Kesalahan Gunnah</p>
        </b-col>
        <b-col sm="12" md="4">
          <h4>{{dataNilaiCalon.madMistake}}</h4>
          <p>Kesalahan Mad</p>
        </b-col>
      </b-row>
      <b-row class="level-tahsin">
        <b-col>
          <p>
            Level tahsin disarankan :
            <strong>{{dataNilaiCalon.levelTahsin}}</strong>
          </p>
        </b-col>
      </b-row>
      <b-row class="note">
        <b-col>
          <label>Catatan:</label>
          <p>{{dataNilaiCalon.note}}</p>
        </b-col>
      </b-row>
    </b-modal>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import router from "@/router";
import User from "@/services/User";

export default {
  name: "DataCalonPeserta",
  data() {
    return {
      periodId: "",
      dataCalon: [],
      dataEvaluatedCalon: [],
      dataNilaiCalon: {
        harakatMistake: "",
        madMistake: "",
        gunnahMistake: "",
        isLulus: "",
        levelTahsin: "",
        catatan: ""
      },
      isModal: false,
      isModalNilai: false,
      isSelector: false,
      modalNilaiStudent: null,
      studentModal: null,
      form: {
        scoreOption: [
          { value: 1, text: "1 Kesalahan" },
          { value: 2, text: "2 Kesalahan" },
          { value: 3, text: "3 Kesalahan" },
          { value: 4, text: "4 Kesalahan" },
          { value: 5, text: "5 Kesalahan" },
          { value: 6, text: "6 Kesalahan" },
          { value: 7, text: "7 Kesalahan" },
          { value: 8, text: "8 Kesalahan" },
          { value: 9, text: "9 Kesalahan" },
          { value: 10, text: "10 Kesalahan" },
          { value: 11, text: "Lebih dari 10 Kesalahan" }
        ],
        graduationOption: [
          { value: true, text: "Lulus" },
          { value: false, text: "Tidak Lulus" }
        ],
        levelTahsinOption: [
          { value: 1, text: "Level 1" },
          { value: 2, text: "Level 2" }
        ],
        harakatMistake: "",
        madMistake: "",
        gunnahMistake: "",
        isLulus: false,
        levelTahsin: 0,
        note: ""
      }
    };
  },
  mounted() {
    User.getLatestPeriod(process.env.VUE_APP_URL);
    if (this.getUserRole[this.getUserRole.length - 1].role_id == 0) {
      this.gotoLoginForbidden();
    } else if (this.getUserRole[this.getUserRole.length - 1].role_id != 4) {
      this.gotoForbiddenPage();
    } else if (!this.getParticipantSelectionPeriodOpened) {
      this.gotoPeriodForbidden();
    } else {
      this.getDataCalon();
      this.getDataNilaiCalon();
    }
  },
  computed: {
    ...mapGetters({
      getCalonPeserta: "getCalonPeserta",
      getAccessToken: "getAccessToken",
      getPeriodId: "getPeriodId",
      getUserId: "getUserId",
      getUserRole: "getUserRole",
      getParticipantSelectionPeriodOpened: "getParticipantSelectionPeriodOpened"
    })
  },
  methods: {
    gotoForbiddenPage() {
      router.push("/forbidden/role");
    },
    gotoLoginForbidden() {
      router.push("/forbidden/login");
    },
    gotoPeriodForbidden() {
      router.push("/forbidden/period");
    },
    toggleModalNilai(dataStudent) {
      this.isModalNilai = !this.isModalNilai;
      this.modalNilaiStudent = dataStudent;
      User.getScoreCandidate(
        process.env.VUE_APP_URL,
        this.getAccessToken,
        this.getPeriodId,
        dataStudent
      ).then(response => {
        this.dataNilaiCalon.harakatMistake = response["harakat_mistake"];
        this.dataNilaiCalon.madMistake = response["mad_mistake"];
        this.dataNilaiCalon.gunnahMistake = response["gunnah_mistake"];
        this.dataNilaiCalon.isLulus = response["is_passed"];
        this.dataNilaiCalon.levelTahsin = response["tahsin_level"];
        this.dataNilaiCalon.note = response["note"];
      });
    },
    toggleModal(dataStudent) {
      this.isModal = !this.isModal;
      this.studentModal = dataStudent;
    },
    handleSubmit(dataStudent) {
      User.sendScoreCandidate(
        process.env.VUE_APP_URL,
        this.getAccessToken,
        this.getPeriodId,
        this.form,
        dataStudent
      ).then(() => {
        this.dataCalon.splice(this.dataCalon.indexOf(dataStudent));
        this.dataEvaluatedCalon.push(dataStudent);
        this.isModal = false;
      });
    },
    getDataCalon() {
      User.getCandidateData(
        process.env.VUE_APP_URL,
        this.getAccessToken,
        this.getPeriodId
      ).then(response => {
        this.dataCalon = response.candidates;
      });
    },
    getDataNilaiCalon() {
      User.getAllCandidateScore(
        process.env.VUE_APP_URL,
        this.getAccessToken,
        this.getPeriodId
      ).then(response => {
        this.dataEvaluatedCalon = response.candidates;
      });
    }
  }
};
</script>

<style lang="scss">
@import "@/styles/form.scss";

.vue-sound-wrapper {
  background: $white;
  padding: 0 $gap-sm-2;
  border-radius: 20px;
  max-width: 100%;

  .vue-sound__player {
    display: flex;
    align-items: center;
  }
}

.vue-sound-wrapper .vue-sound__player a {
  color: $primary;
}

.vue-sound-wrapper
  .vue-sound__player
  .vue-sound__playback-time-wrapper
  .vue-sound__playback-time-indicator {
  background: black !important;
}

.vue-sound-wrapper .vue-sound__player .vue-sound__playback-time-wrapper > span {
  display: none;
}

.vue-sound-wrapper
  .vue-sound__player
  .vue-sound__extern-wrapper
  .icon-spinner11 {
  display: none;
}

.vue-sound-wrapper
  .vue-sound__player
  .vue-sound__extern-wrapper
  .icon-volume-high {
  display: none;
}

.vue-sound-wrapper
  .vue-sound__player
  .vue-sound__extern-wrapper
  .volume-toggle {
  display: none;
}

.data-calon-container {
  min-height: inherit;
}

.student-name {
  text-align: center;
  margin-bottom: $gap-sm-4;
}

.level-tahsin {
  margin-top: $gap-sm-4;
}

.score {
  align-items: center;
  justify-content: center;
  text-align: center;
}

.graduation {
  text-align: center;
  margin-bottom: $gap-sm-4;

  .primary {
    color: $primary;
  }

  .danger {
    color: $danger;
  }
}

.note {
  margin-top: $gap-m-1;
  label {
    font-weight: bold;
    margin-bottom: $gap-sm-1;
  }

  p {
    word-break: break-all;
    white-space: pre-wrap;
  }
}

.header-title {
  text-align: center;
  color: #000;
  margin-top: $gap-m-2;
  margin-bottom: $gap-m-2;
}

.hero-message {
  max-width: 20rem;
}

.table-container {
  min-width: 20%;
  max-height: 40%;
  padding-left: 10%;
  padding-right: 10%;
}

#table-calon-peserta {
  border-collapse: collapse;
  width: 100%;
  text-align: center;
  vertical-align: center;
}

#table-calon-peserta td,
#table-calon-peserta th {
  border: 1px solid #ddd;
  padding: 8px;
}

#table-calon-peserta th {
  padding-top: 12px;
  padding-bottom: 12px;
  background-color: $primary;
  color: white;
}
</style>
