<template>
  <div>
    <img class="vector svg-1" src="@/assets/img/Vector 8.png" alt="vector-8" />
    <img class="vector svg-2" src="@/assets/img/Vector 9.png" alt="vector-9" />
    <img class="vector svg-3" src="@/assets/img/Vector 10.png" alt="vector-10" />
    <img class="vector svg-4" src="@/assets/img/Vector 11.png" alt="vector-11" />
    <img class="vector svg-5" src="@/assets/img/Vector 12.png" alt="vector-12" />
    <img class="vector svg-6" src="@/assets/img/Vector 13.png" alt="vector-13" />
    <img class="vector svg-7" src="@/assets/img/Vector 8.png" alt="vector-8" />
    <img class="vector svg-8" src="@/assets/img/Vector 9.png" alt="vector-9" />
    <section class="hero-container">
      <h1 class="header-title">
        Pendaftaran TahfidzQu
        <span class="isOpened" v-if="getRegistrationPeriodOpened">Dibuka</span>
        <span class="isClosed" v-else>Ditutup</span>
      </h1>
      <p class="header-description">{{ getHeaderDescription }}</p>
      <router-link to="/program-registration">
        <button
          v-if="getRegistrationPeriodOpened"
          class="daftar-program btn primary mt-4"
        >Daftar Sekarang !</button>
      </router-link>
    </section>
    <section class="timeline-container">
      <h2 class="header-title">Timeline</h2>
      <h3 class="description">
        Timeline TahfizhQu Angkatan ke-
        <span class="generation">{{ getTimeline.generation }}</span>
      </h3>
      <p class="timeline-details">
        <span class="title">Pendaftaran dan Tes Peserta Baru</span>
        <br />Hingga
        <span class="dueDate">{{ getTimeline.dueDate }}</span>
        <br />
        <span class="title">Pengumuman dan Pemilihan Jadwal</span>
        <br />
        <span class="announcement">{{ getTimeline.announcement }}</span>
        <br />
        <span class="title">Pembayaran</span>
        <br />hingga
        <span class="paymentDate">{{ getTimeline.paymentDate }}</span>
        <br />
        <span class="title">Mulai Kelas</span>
        <br />
        <span class="classStartDate">{{ getTimeline.classStartDate }}</span>
        <br />
        <span class="title">Penutupan TahfizhQu 9</span>
        <br />
        <span class="programClosingDate">{{ getTimeline.programClosing }}</span>
      </p>
    </section>
    <section class="curriculum-container">
      <h2 class="header-title">Kurikulum</h2>
      <p class="description">{{ getCurriculumDescription }}</p>
      <div class="card-container">
        <div class="card" v-for="(data, id) in getAllCurriculums" :key="id">
          <p>{{ data.content }}</p>
        </div>
      </div>
    </section>
    <section class="teacher-container">
      <h2 class="header-title">Pengajar</h2>
      <div class="card-container">
        <div class="card" v-for="(data, index) in getAllTeachers" :key="index">
          <div class="profile-image">
            <img
              class="default-profile"
              src="@/assets/img/profile-img.png"
              alt="default-profile-img"
            />
            <img :src="data.image" v-if="data.image != ''" alt="teacher-profile" />
          </div>
          <h4 class="teacher-name">{{ data.name }}</h4>
          <p class="teacher-details">
            <strong>Pendidikan Terakhir:</strong>
            <br />
            <span>{{ data.lastEducation }}</span>
            <br />
            <strong>Aktivitas:</strong>
            <br />
            <span>{{ data.activity }}</span>
            <br />
          </p>
          <div class="contact-container">
            <a :href="data.contact.facebook">Facebook</a>
            <a :href="data.contact.linkedin">Linkedin</a>
          </div>
        </div>
      </div>
    </section>
    <section class="facilities-container">
      <h2 class="header-title">Fasilitas</h2>
      <div class="card-container">
        <div class="card" v-for="(data, id) in getAllFacilities" :key="id">
          <div class="card-image">
            <img :src="data.image" alt="card-image" />
          </div>
          <p class="facility">{{ data.content }}</p>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import { mapGetters } from "vuex";
import User from "@/services/User";

export default {
  name: "Home",
  computed: {
    ...mapGetters([
      "getRegistrationPeriodOpened",
      "getTimeline",
      "getHeaderDescription",
      "getCurriculumDescription",
      "getAllTeachers",
      "getAllCurriculums",
      "getAllFacilities",
      "getShowAlertError",
      "setShowAlertError"
    ])
  },
  created() {
    User.getLatestPeriod(process.env.VUE_APP_URL);
  }
};
</script>

<style lang="scss" scoped>
@import "@/styles/home.scss";
</style>
