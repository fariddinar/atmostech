<template>
  <div id="main" v-cloak>
    <div class>
      <img class="logo" src="src/assets/logo.png" alt />

      <h1 class="heading">Dimmensionner votre pompe !</h1>
      <span class="services__item">
        Débit journalier requis
        <input v-model="Dj" name value />
      </span>
      <span class="services__item">
        Nombre d'heures d'ensoleillement crête
        <input v-model="Nh" name value />
      </span>
      <span class="services__item">
        Hauteur de pompage
        <input v-model="H" name value />
      </span>
      <span class="services__item">
        Diamètre de la tuyauterie
        <input v-model="D" name value />
      </span>
      <span class="services__item">
        Longueur de la tuyauterie
        <input v-model="L" name value />
      </span>
      <span class="services__item">
        Nombre de coudes 90°
        <input v-model="Nc" name value />
      </span>
      <span class="services__item">
        Nombre de vannes
        <input v-model="Nv" name value />
      </span>
      <span class="services__item">
        Rigosité conduite
        <input v-model="Rc" name value />
      </span>
      <span class="services__item">
        coeff a prendre
        <input v-model="C" name value />
      </span>
      <span class="services__item">
        Nombre
        <input v-model="N" name value />
      </span>
      <span class="services__item">
        Ks
        <input v-model="Ks" name value />
      </span>

      <!--ul class="services">
      <li class="services__item" v-for="service in services" v-on:click="toggleActive(service)" v-bind:class="{ 'active' : service.active }">
        <span class="services__item">{{service.name}}
          <input placeholder="" v-model="Dj" name="" value=""></span>
      <li>


      </ul-->
      <div>
        <p class="total">
          <span>Hauteur manométrique (HMT) :</span>
          <span>{{ HMT() }}</span>
        </p>
        <p class="total">
          <span>Débit :</span>
          <span>{{ Debit() }}</span>
        </p>
        <p class="total">
          <span>Vitesse :</span>
          <span>{{ Vitesse() }}</span>
        </p>
        <p class="total">
          <span>Nombre de Reynolds :</span>
          <span>{{ Reynolds() }}</span>
        </p>
        <p class="total">
          <span>Coefficient (blasius) :</span>
          <span>{{ Blasius() }}</span>
        </p>
        <p class="total">
          <span>Coefficient (Colebrook) :</span>
          <span>{{ Colebrook() }}</span>
        </p>
        <p class="total">
          <span>Erreur :</span>
          <span>{{ Erreur() }}</span>
        </p>
        <p class="total">
          <span>Pertes régulières :</span>
          <span>{{ Regular() }}</span>
        </p>
        <p class="total">
          <span>Pertes singulières :</span>
          <span>{{ Singular() }}</span>
        </p>
      </div>
      <br />
      <br />
      <div>
        <p style="margin-left:26%; font-size:20px;" class>Exporter vos résultats sous format pdf</p>
        <br />
        <button class="button" @click="exportPdf">EXPORTER ICI</button>
      </div>
    </div>
  </div>
</template>

<script>
import { jsPDF } from "jspdf";
import "jspdf-autotable";

export default {
  components: {},
  data() {
    return {
      Dj: "",
      Nh: "",
      H: "",
      D: "",
      L: "",
      Nc: "",
      Nv: "",
      db: "",
      Rey: "",
      bla: "",
      Rc: "",
      C: "",
      ks: "",
      N: "",
      hmt: ""
    };
  },
  methods: {
    HMT: function() {
      this.hmt = parseFloat(this.Dj) + 3;
      return this.hmt;
    },
    Debit: function() {
      this.db = parseFloat(this.Dj) / parseFloat(this.Nh);
      return this.db;
    },
    Vitesse: function() {
      this.V = (4 * this.db) / (3.141592 * Math.pow(this.D, 2) * 3600);
      return this.V;
    },
    Reynolds: function() {
      this.Rey = 1000 * this.V * this.D * 1000;
      return this.Rey;
    },
    Blasius: function() {
      this.bla = 0.3164 * Math.pow(this.Rey, -0.25);
      return this.bla;
    },
    Colebrook: function() {
      this.cole = Math.pow(
        -2 *
          Math.log(
            (2.51 * Math.sqrt(this.bla)) / this.Rey +
              (this.Rc * 0.001) / (3.71 * this.D)
          ),
        -2
      );
      return this.cole;
    },
    Erreur: function() {
      this.Err = Math.abs(this.cole - this.bla);
      return this.Err;
    },
    Regular: function() {
      this.Reg = (this.C * this.L * Math.pow(this.V, 2)) / (2 * 9.81 * this.D);
      return this.Reg;
    },
    Singular: function() {
      this.Sing =
        (Math.pow(this.V, 2) *
          (1.13 * this.Nc + 1.7 * this.Nv + this.N * this.Ks)) /
        (2 * 9.81);
      return this.Sing;
    },
    exportPdfs: function() {
      var doc = new jsPDF();
      doc.text("INPUTS", 10, 10);

      doc.text(this.H, 10, 20);
      doc.text(this.Dj, 10, 30);
      doc.save("test number.pdf");
    },
    exportPdf: function() {
      var doc = new jsPDF();
      doc.autoTable({
        head: [["Donnés", "Valeurs"]],
        body: [
          ["debit jornalier", this.Dj],
          ["Nombre d'heurs", this.H],
          ["Débit journalier requis", this.Dj],
          ["Nombre d'heures d'ensoleillement crête", this.Nh],
          ["Hauteur de pompage", this.H],
          ["Diamètre de la tuyauterie", this.D],
          ["Longueur de la tuyauterie", this.L],
          ["Nombre de coudes 90°", this.Nc],
          ["Nombre de vannes", this.Nv],
          ["Nombre", this.N],
          ["Ks", this.Ks]
        ]
      });

      doc.text("Resultats", 100, 111);

      doc.autoTable({
        //margin:{top: 200},
        head: [["Donnés", "Valeurs"]],
        body: [
          ["Hauteur manométrique (HMT)", this.hmt],
          ["Débit", this.D],
          ["Vitesse", this.V],
          ["Nombre de Reynolds", this.Rey],
          ["Coefficient (blasius)", this.bla],
          ["Coefficient (Colebrook)", this.cole],
          ["Erreur", this.Err],
          ["Pertes régulières", this.Reg],
          ["Pertes singulières", this.Sing]
        ]
      });
      doc.save("test number.pdf");
    }
  }
};
</script>

<style scoped="">
*,
*:before,
*:after {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
#main {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: "Open Sans", sans-serif;
  color: #fff;
  background-color: #37718e;
}
.heading {
  font-family: cocogoose;
  font-size: 2.5rem;
  /*-webkit-font-smoothing: antialiased;*/
  letter-spacing: 0.01rem;
  text-align: top center;
  text-shadow: 0 3px 0 #295469;
  margin-bottom: 10px;
}
.services__item {
  display: flex;
  justify-content: space-between;
  padding: 1rem;
  border-radius: 0.25rem;
  box-shadow: 1 3px 3px 0 #295469;
  color: #006080;
  background-color: #aef3e7;
  margin-bottom: 3px;
}
.services__item.active {
  color: #fff;
  background-color: #c33c54;
}
.total {
  display: flex;
  justify-content: space-between;
  padding: 1rem;
  border-top: 1px solid #fff;
}
.button {
  display: flex;
  padding: 0.5rem;
  border-radius: 1.25rem;
  color: #006080;
  background-color: #aef3e7;
  margin-bottom: 40px;
  cursor: pointer;
  margin-left: 40%;
  margin-right: -10%;
}
.logo {
  width: 250px;
  position: inherit;
  margin-left: 30%;
}
</style>
