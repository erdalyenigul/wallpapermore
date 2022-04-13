<template>
  <div class="header">
      <h1 class="welcomeHead">WALLPAPER MORE! <br> Free wallpapers for everyone!</h1>
      <h2 class="welcomeMsg">
        A Huge collection of stunning HD wallpapers that you can find easily on this app, <br>change your device's background with thousands of choices and categories of HD Wallpepers. <br> Just search and download. Its totally free!
      </h2>
    </div>
</template>

<style lang="scss">
  .header{display:flex; flex-wrap:wrap; justify-content:center; width:100%; margin-bottom:20px; padding:20px 40px; background-color: #007bff; color: #fff;
    .welcomeHead{display:flex; justify-content: center; width:100%; margin-bottom: 20px; font-size:30px; line-height: 36px; text-align:center;}
    .welcomeMsg{display:flex; justify-content: center; width:100%; font-size:16px; line-height: 22px; text-align:center;}
  }
  @media (max-width:767px) {
    .header{padding:20px; border-radius:0;}
    .header .welcomeHead{font-size:22px; line-height:26px; margin-bottom:0;}
    .header .welcomeMsg{display:none;}
  }
</style>
