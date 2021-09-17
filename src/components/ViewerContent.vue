<template>
  <div>
    <div
      centered
      style="text-align: center; padding: 10px"
      v-if="Math.min.apply(null, show) >= 1"
    >
      <v-btn small v-on:click="beforeContent()" rounded>▲前のお話▲</v-btn>
    </div>
    <div v-for="i in show" :key="i" :id="'page-' + i">
      <img
        v-for="(_, j) in pages[i].ImagesUrl"
        :key="j"
        :src="pages[i].ImagesUrl[j]"
        width="100%"
      />
      <div style="display: flex; justify-content: center">
        <div style="text-align: center">
          <v-btn rounded x-small v-on:click="scrollTop()" depressed>
            トップに戻る
          </v-btn>
        </div>
      </div>
    </div>
    <div centered style="text-align: center; padding: 10px">
      <v-btn
        rounded
        small
        v-on:click="addContent()"
        v-if="pages.length - 1 > Math.max.apply(null, show)"
      >▼続きを表示▼</v-btn>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    page: {
      type: String,
      default: "1",
    }
  },
  data: () => ({
    show: [],
    pages: window.pageData,
    bottom: false,
  }),
  methods: {
    /** ページの下が見えているのか判定するための関数 */
    bottomVisible() {
      const scrollY = window.scrollY; //縦スクロールの量
      const visible = window.innerHeight; //ブラウザの内側の高さ
      const pageHeight = document.documentElement.scrollHeight; //ページの高さ
      //ほぼページ最下部までスクロールされていたらTrueを返す(2は調整のマージン)
      const bottomOfPage = visible + scrollY + 2 >= pageHeight;
      return bottomOfPage || pageHeight < visible;
    },
    /** 画像を追加するための関数 */
    addContent(addnum = 1) {  
      if (this.pages.length - 1 > Math.max.apply(null, this.show)) {
        this.show.push(Math.max.apply(null, this.show) + addnum);
      } else {
        window.console.log("show.length is over pages.length")
      }
    },
    /** 前の話数を表示するための関数 */
    beforeContent() {
      if (Math.min.apply(null, this.show) >= 1) {
        this.show.unshift(Math.min.apply(null, this.show) - 1);
      } else {
        window.console.log("can not show page under 0")
      }
    },
    /** 画面の一番上までスクロールするための関数 */
    scrollTop() {
      window.scrollTo(0, 0);
    },
    /** スクロールイベントのリスナー用関数. */
    onScroll() {
      this.bottom = this.bottomVisible();
    },
    /** 表示する画像をセットするための関数(指定話数を表示させるためのもの) */
    setShow() {
      const pageInt = parseInt(this.page); //文字列のpageをIntegerにして変数pageIntに格納
      if (this.page === "latest") {
        //page=latestの場合は最新話を表示させる
        this.show.push(this.pages.length - 1);
      } else if (pageInt >= 1 && pageInt <= this.pages.length) {
        //pageが1< page <= pages.lengthの場合は指定話数を表示させる
        this.show.push(pageInt - 1);
      } else {
        //page <= 0の場合やそれ以外ケースでは、とりあえず0(1話目)を表示させる
        this.show.push(0);
      }
    },
    initShow() {
      this.show = [];
      this.setShow();
    },
  },
  watch: {
    bottom() {
      if (this.bottom) {
        this.addContent();
      }
    },
    page() {
      this.initShow();
    },
  },
  created() {
    //スクロールイベントのリスナーにonScroll関数を追加
    window.addEventListener("scroll", this.onScroll);
    this.setShow(); //表示する画像をセット
  },
  destroyed() {
    window.removeEventListener("scroll", this.onScroll);
  },
};
</script>