<template>
  <v-app>
    <v-app-bar app color="primary" dark>
      <span class="text-h5">エントリーシート記述用メモ</span>
    </v-app-bar>

    <v-main>
      <v-container class="my-5">
        <!-- メモ入力欄 -->
        <v-row no-gutters v-for="(memoItem, index) in memos" :key="index">
          <v-col>
            <v-text-field
              v-model="memoItem.title"
              outlined
              :label="'タイトル ' + (index + 1)"
            ></v-text-field>
          </v-col>
          <v-col>
            <v-textarea
              v-model="memoItem.content"
              outlined
              rows="5"
              :label="'メモ ' + (index + 1)"
              @input="updateCharactercount(index)"
            ></v-textarea>
            <!-- <p>現在の文字数:{{ memoItem.characterCount }}</p> -->
            <!-- <v-text-field
              v-if="memoItem.characterCount >= 0"
              :label="null"
              :value="'現在の文字数: ' + memoItem.characterCount"
              append-outer-icon="mdi-counter"
              readonly
            ></v-text-field> -->
            <v-row>
              <v-col style="margin-top: -40px;">
                <v-text-field
                  v-if="memoItem.characterCount >= 0"
                  :label="null"
                  :value="'文字数(スペースを含む): ' + memoItem.characterCount"
                  append-outer-icon="mdi-counter"
                  readonly
                  style="width: 100%"
                ></v-text-field>
              </v-col>
              <v-col  style="margin-top: -40px;">
                <v-text-field
                  v-if="memoItem.nolinebreak_wordCount >= 0"
                  :label="null"
                  :value="'文字数(スペースを含まない):' + memoItem.nolinebreak_wordCount"
                  append-outer-icon="mdi-counter"
                  readonly
                  style="width: 140%"
                ></v-text-field>
                <!-- <v-btn @click="convertHankaku2Zenkaku(index)">全角変換</v-btn> -->
              </v-col>
            </v-row>
            <v-row style="margin-top: -30px; margin-bottom: 30px;">
            <!-- <v-row style="margin-bottom: 15px;">  -->
              <v-col>
                <v-btn @click="convertHankaku2Zenkaku(index)">全角変換</v-btn>
              </v-col>
              <v-col>
                <v-btn @click="convertZenkaku2Hankaku(index)">半角変換</v-btn>
              </v-col>
              <v-col>
                <v-btn @click="copyToClipboard(index)">コピー</v-btn>
              </v-col>
            </v-row>
            <!-- <v-row>
              <v-divider></v-divider>
            </v-row> -->
          </v-col>
        </v-row>

        <!-- ボタン -->
        <v-row no-gutters class="mt-2">
          <v-col>
            <v-btn @click="addNewMemo">新しいメモを追加</v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>


<script>
import {hankana2Zenkana,haneisu2Zeneisu,Zenkana2hankana,Zeneisu2haneisu} from './main.js';

export default {
  data() {
    return {
      memos: [{ title: '', content: '' ,characterCount: 0,nolinebreak_wordCount: 0}], // 最初のメモを持つ配列
    };
  },
  methods: {
    addNewMemo() {
      // 新しいメモを追加
      this.memos.push({ title: '', content: '' ,characterCount: 0,nolinebreak_wordCount: 0});
    },
    updateCharactercount(index){
      const content = this.memos[index].content;
      this.memos[index].characterCount = content.replace(/\n/g, "").length;
      this.memos[index].nolinebreak_wordCount = content.replace(/\s| /g, "").length;
    },
    // convertHankaku2Zenkaku(index){
    //   const content = this.memos[index].content;
    //   zen_eisu = moji(content).convert("HEtoZE").toString(); //半角英数to全角
    //   zen_kana = moji(zen_eisu).convert("HKtoZK").toString(); //半角カナto全角
    //   content = zen_kana;
    // },
    convertHankaku2Zenkaku(index){
      var convert_kana = hankana2Zenkana(this.memos[index].content); //半角カナto全角
      var convert_eisu = haneisu2Zeneisu(convert_kana);//半角英数to全角
      this.memos[index].content = convert_eisu
    },
    convertZenkaku2Hankaku(index){
      var convert_kana = Zenkana2hankana(this.memos[index].content); //全角カナto半角
      var convert_eisu = Zeneisu2haneisu(convert_kana); //全角英数to半角
      this.memos[index].content = convert_eisu
    },
    copyToClipboard(index){
      navigator.clipboard.writeText(this.memos[index].content)
      .then(() => {
        console.log("copied")
      })
      .catch(e => {
        console.error(e)
      })
    },
  },
};
</script>
