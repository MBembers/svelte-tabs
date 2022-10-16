<script>
  export let isOn;
  export let title;
  export let message;
  export let hideModal;

</script>

<div class="overlay" style="display: {isOn ? "flex" : "none"};" on:click={hideModal}>
  <div class="modal">
    <div class="header">{title}</div>
    <div class="content">
      {message}
    </div>
    
  </div>
</div>

<style>
    .overlay {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 2;
    justify-content: center;
  }
  .modal {
    margin-top: 50px;
    background-color: white;
    height: 50%;
    width: 30%;
  }
  .header {
    height: 10%;
    font-size: 90%;
  }
  
</style>