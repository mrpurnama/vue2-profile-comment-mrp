<template>
    <div class="card">
        <!-- <v-img src="profileImage" :alt="name" /> -->
        <img src="../assets/mrp.png" :alt="name" style="width:100%">
        <h3>{{ name }}</h3>
        <p class="title">{{ description }}</p>
        <p>Front-end Developer, mainly use JS</p>
        <a href="tel:+6289621372302">Phone: +62 89621372302</a>
        <div style="margin: 24px 0;" class="soc-med">
            <a href="https://gitlab.com/mrpurnama" target="_blank" style="color: orange;"><i class="fa fa-gitlab"></i></a> 
            <a href="https://github.com/mrpurnama" target="_blank"><i class="fa fa-github"></i></a> 
        </div>
        <p style="margin-bottom: 0;"><a href="./assets/cv.pdf" class="download-cv" download>Get CV</a></p>
    </div>
</template>

<script>
export default {
    name: 'Profile',
    props: {
        name: {
            type: String,
            default: 'Mochamad Rizky Purnama'
        },
        description: {
            type: String,
            default: '" Love what you DO "'
        },
    }
}
</script>

<style scoped>
    .card {
      box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
      max-width: 300px;
      margin: 16px auto 0;
      text-align: center;
      font-family: arial;
      /* color: #000; */
    }

    .title {
      color: grey;
      font-size: 18px;
    }

    .card .download-cv {
      border: none;
      outline: 0;
      display: inline-block;
      padding: 8px;
      color: white;
      background-color: #000;
      text-align: center;
      cursor: pointer;
      width: 100%;
      font-size: 18px;
    }

    a {
      text-decoration: none;
    }

    .card .soc-med a {
      font-size: 22px;
      color: black;
      margin-right: 8px;
    }

    .card button:hover, a:hover {
      opacity: 0.7;
    }

</style>