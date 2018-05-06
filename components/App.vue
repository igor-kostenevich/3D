<template>
 <div class="wrapp">
  <el-row type="flex" :gutter="5" id="app" class="stretch">
  <div class="download">
            <p class="btn_download__text">Создать свой дизайн</p>
            <div class="fix__btn_downdload">
                <el-button class="btn_downdload" type="text" @click="download"> Готово </el-button>
            </div>
         </div>
    <el-col :span="18" class="stretch">
      <div class="tool horizontal space around download"></div>
      <el-row type="flex" :gutter="5" class ="grow">
        <el-col :span="2">
        </el-col>
        <el-col :span="14">
        <div class="tool vertical">
            <div class="fix_button">
                <el-button id="fix-fs-button" type="text" >
                    <el-upload id="upload"  drag action="false" :http-request="empty" :before-upload="upload" :show-file-list="false" accept="image/*">  
                      <span id="fix-upload"><i class="el-icon-upload"></i></span>
                  </el-upload>
                 </el-button>
            </div>
            <div class="fix_button">
                <el-dropdown @command="changeFontType" :hide-on-click="false">
                  <el-button type="text" :class="onText && 'selected'" @click="changeMode('text')">
                    <i class="fa fa-font"></i> Новый текст
                  </el-button>
                 
                </el-dropdown>
            </div>
            <div class="fix_button">
                <el-button :class="onDraw && 'selected'" type="text" icon="fa fa-paint-brush" @click="changeMode('draw')"> Кисть </el-button>
            </div>
    
            <div class="fix_button">
                <el-button type="text">
                    <i class="figures"></i>
                    <span>Фигуры</span>
                </el-button>
                
                <!-- <el-button type="text" icon="fa fa-trash" @click="drawer.drop()"> Clear </el-button> -->
            </div>
          </div>
          <div id="drawer" ref="drawer">
            <canvas id="d2" ref="d2"
              @mousedown="start($event)"
              @mousemove="action($event)"
              @dblclick="focus($event)"
              @mouseup="stop($event)"
              @mouseout="stop($event)">
            </canvas>
          </div>
        </el-col>
        
      </el-row>
        <div class="text_click">
            <p class="text__user">Кликните <span class="text-blue">2</span> раза по картинке/ тексту, чтобы <span class="text-blue">переместить/ повернуть</span></p>
        </div>
        
        <div class="tool__user">
                <p class="user__zoom__text">Масштаб</p>
               <div class="tool__user__zoom">
                    <input type="number" class="user__zoom" name="t" value="35" min="0" max="100">
               </div>
               <p class="user__zoom__text-rotate">Вращать</p>
                <div class="user__rotate">
                    <i class="fa fa-search-plus"></i>
                    <i class="fa fa-search-minus"></i> 
                </div>
                
               <span class="tool__user__border"></span>
               
                 <el-select  :disabled="!onText" v-model="font.family" size="mini">
                    <el-option  v-for="font in fonts" :key="font" :label="font" :value="font" :style="`font-family: ${font}`" />
                </el-select>
                
                <div class="container_dropdown_menu">
                 <el-dropdown-menu class="fix__display" slot="dropdown">
                    <el-dropdown-item :class="font.type.bold && 'selected'" command="bold">
                      <i class="fa fa-bold"></i> Bold
                    </el-dropdown-item>
                    <el-dropdown-item :class="font.type.italic && 'selected'" command="italic">
                      <i class="fa fa-italic"></i> Italic
                    </el-dropdown-item>
                    <el-dropdown-item>
                        <p class="AV">AV</p>
                    </el-dropdown-item>
                  </el-dropdown-menu>
                </div>
              <div class="container-input-number"> 
                   <p class="small__T">T</p>
                   <p class="big__T">T</p>
                   <el-input-number :disabled="!onText && !onDraw" v-model="number" :min="1" size="mini" >
                   
                   </el-input-number>
               </div>
               
               <span class="tool__user__border"></span>
               
               <span class="block title">
                
                <el-color-picker v-model="color" size="mini"></el-color-picker>
               </span>
               
               
               <div class="color-user--button">
                    <p class="user__zoom__text">Цвет фона</p><br>
                    <span class="color_text__button">Новый</span>
                    
                    <span class="transperant__block"></span>
                    <span class="blocking"></span>
                    <span class="color_text__button__old">текущий</span>
                    
                    <div class="button__success">
                        <span class="color_text__transparent">Прозрачный</span>
                        <span class="transparent__background"></span>
                        
                    </div>
               </div>
            
            
        </div>
        
    </el-col>
    <el-col :span="6" class="column">
    <div class="fix-3d_tool">
          <div class="tool horizontal space around">
            <div class="button-fix-mini3d">
            
                <el-button class="fix-button__mini" type="text" icon="fa fa-share" @click="cover"> Просмотр <br> на модели</el-button>
          
                <el-button class="fix-button__mini" type="text" icon="fa fa-pause" @click="animate(false)" v-if="preview.animation"> Старт / <br> Стоп</el-button>
                <el-button class="fix-button__mini" type="text" icon="fa fa-play" @click="animate(true)" v-else> Старт / <br> Стоп</el-button>
                
               
                <el-button class="fix-button__mini" type="text" icon="fa fa-trash" @click="preview.clear()"> Очистить <br> модель </el-button> 
                </div>
                <!--<span class="block title">
                  <el-color-picker v-model="sceneColor" size="mini" @change="changeSceneColor"></el-color-picker>
                  <span class="title"> Scene </span>-->
                </span>
              </div>
              <div id="previewMini" ref="previewMini">
                <canvas id="mini3d" ref="mini3d" @click="show">
                  Sorry your browser doesn't seem to support webgl! :(
                </canvas>
          </div>
      </div>
      
      <el-dialog
        custom-class="column"
        :visible.sync="dialogVisible"
        :fullscreen="true">
        <span slot="title">
          <order-form :source="source" />
        </span>
        <div id="previewMax" ref="previewMax">
          <canvas id="max3d" ref="max3d"
            @mousedown="grab($event)"
            @mousemove="rotate($event)"
            @mouseup="release($event)">
          </canvas>
        </div>
        <span slot="footer">
          <el-button icon="fa fa-pause" @click="animate(false)" v-if="preview.animation"> Pause </el-button>
          <el-button icon="fa fa-play" @click="animate(true)" v-else> Play </el-button>
          <el-button icon="fa fa-share" @click="cover"> Cover </el-button>
          <el-button icon="fa fa-download" @click="download"> Download </el-button>
          <el-button icon="fa fa-trash" @click="preview.clear()"> Clear </el-button>
          <el-button @click="dialogVisible = false"> Cancel </el-button>
        </span>
      </el-dialog>
      <div class="btn-help">
            <el-button class="button--trigger" type="text" icon="fa fa-undo"> Назад </el-button> 
            <el-button class="button--trigger" type="text" icon="fa fa-undo fa-undo__transform"> Вперед </el-button>
            <span class="line_y"></span>
            <div class="grid"><input id="input--checkbox" type="checkbox"><span id="grid">Сетка</span></div>
      </div>
      <transition-group name="flip-list" tag="ul" class="el-upload-list el-upload-list--picture grow">
        <li v-for="(layer, index) in layers" :key="layer.uid" class="el-upload-list__item" :class="selected == index && 'selected'">
          <img
            :src="layer.type == 'picture' ? layer.src : `../assets/img/${layer.type}.png`"
            :alt="layer.name"
            class="el-upload-list__item-thumbnail"
            @click="choose(index)"
          />
          <div class="name">{{ layer.name }}</div>
          <el-button class="button--movingLayers" type="text" icon="fa fa-chevron-up" :disabled="index === 0" @click="raise(index)" />
          <el-button class="button--movingLayers" type="text" icon="fa fa-chevron-down" :disabled="index === layers.length - 1" @click="lower(index)" />
          <i class="fa fa-eye"></i>
          <i class="el-icon-close" @click="remove(index)"></i>
        </li>
      </transition-group>
      <div class="rules">
            <p class="designations">Обозначения</p>
            <hr class="rules__line">
            <p id="rules__print" class="rules-designations--text">Безопасная область <br> печати
                <span class="green__line"></span>
            </p>
            <p class="rules-designations--text">Линия обрезки <span class="red__line" height="1"></span></p>
      </div>
    </el-col>
  </el-row>
  </div>
</template>

<script>
  import Drawer from '../lib/Drawer'
  import Preview from '../lib/Preview'
  import OrderForm from './OrderForm.vue'
  
  

  export default {
    name: 'app',
    components: {
      OrderForm
    },
    data() {
      return {
        dialogVisible: false,
        fonts: [
          'Arial',
          'Comic Sans MS',
          'Georgia',
          'Courier New',
          'Impact',
          'Tahoma',
          'Consolas',
          'Times New Roman'
        ],
        font: {
          color: '#ff0000',
          type: {
            bold: false,
            italic: false
          },
          family: 'Arial',
          size: 45
        },
        line: {
          color: '#54d595',
          width: 100
        },
        sceneColor: '#ffffff',
        animation: false,
        mode: 0b0001, /// draw, text, resize, move
        selected: -1,
        preview: {},
        drawer: {},
        layers: []
      }
    },
    watch: {
      'drawer.layers.observable'() {
        this.layers = this.drawer.layers.array;
      }
    },
    computed: {
      onDraw() {
        return this.mode & 0b1000;
      },
      onText() {
        return this.mode & 0b0100;
      },
      onResize() {
        return this.mode & 0b0010;
      },
      onMove() {
        return this.mode & 0b0001;
      },
      number: {
        get() {
          return this.onText ? this.font.size : this.line.width;
        },
        set(number) {
          if (this.onText) {
            this.font.size = number;
          } else {
            this.line.width = number;
          }
        }
    
      },
      color: {
        get() {
          return this.onText ? this.font.color : this.line.color;
        },
        set(color) {
          if (this.onText) {
            this.font.color = color;
          } else {
            this.line.color = color;
          }
        }
      }
    },
    
    methods: {
      async download() {
        const blob = await this.preview.export();
        const url = window.URL.createObjectURL(blob);
        location.replace(url);
      },
      animate(animation) {
        this.preview.animation = this.animation = animation;
      },
      changeBaseColor() {
        this.preview.baseColor = this.baseColor;
      },
      changeSceneColor() {
        this.preview.sceneColor = this.sceneColor;
      },
      changeFontType(name) {
        this.font.type[name] = !this.font.type[name];
      },
      changeMode(mode) {
        switch (mode) {
          case 'draw':
            this.mode = 0b1000;
            this.drawer.defocus();
            this.selected = -1;
            break;
          case 'text':
            this.mode = 0b0100;
            this.drawer.defocus();
            this.selected = -1;
            break;
          case 'resize':
            this.mode = 0b0011;
            break;
          case 'move':
            this.mode = 0b0001;
            this.drawer.defocus();
            this.selected = -1;
            break;
        }
      },
      async upload(file) {
        await this.drawer.upload(file);
        this.drawer.focus(0);
        this.selected = 0;
        this.changeMode('resize');
      },
      cover() {
        this.preview.base64 = this.drawer.source;
      },
      source() {
        return this.drawer.source;
      },
      remove(layer) {
        if (layer == this.selected) {
          this.selected = -1;
        }
        this.drawer.remove(layer);
      },
      raise(layer) {
        if (this.selected == layer) {
          this.selected -= 1;
        } else if (this.selected + 1 == layer) {
          this.selected += 1;
        }
        this.drawer.raise(layer);
      },
      lower(layer) {
        if (this.selected == layer) {
          this.selected += 1;
        } else if (this.selected - 1 == layer) {
          this.selected -= 1;
        }
        this.drawer.lower(layer);
      },
      choose(layer) {
        if (this.selected == layer) {
          this.drawer.defocus(layer);
          this.selected = -1;
          this.changeMode('move');
        } else {
          this.drawer.focus(layer);
          this.selected = layer;
          this.changeMode('resize');
        }
      },
      focus(event) {
        const layer = this.drawer.layer(event.offsetX, event.offsetY);
        this.drawer.focus(layer);
        this.selected = layer;
        this.changeMode('resize');
      },
      conversion(index) {
        this.drawer.conversion(index);
      },
      start(event) {
        if (this.onDraw) {
          return this.mouse = this.drawer.helpers.draw(
            event.offsetX,
            event.offsetY,
            { style: this.line }
          );
        }
        if (this.onText) {
          this.keyboard = this.drawer.helpers.text(
            event.offsetX,
            event.offsetY,
            { style: this.font }
          );
          return this.keyboard.next();
        }
        if (this.onResize) {
          this.mouse = this.drawer.helpers.resize(event.offsetX, event.offsetY);
          if (this.mouse.next().done) {
            this.mouse = this.drawer.helpers.rotate(event.offsetX, event.offsetY);
            if (!this.mouse.next().done) {
              return;
            }
          } else {
            return;
          }
        }
        if (this.onMove) {
          this.mouse = this.drawer.helpers.move(event.offsetX, event.offsetY);
        } 
      },
      action(event) {
        if (this.mouse) {
          this.mouse.next({ x: event.offsetX, y: event.offsetY });
        } else {
          this.hover.next({ x: event.offsetX, y: event.offsetY });
        }
      },
      stop(event) {
        if (this.mouse) {
          this.mouse.next();
          this.mouse = false;
          if (this.onDraw) {
            this.drawer.focus(0);
            this.selected = 0;
            this.changeMode('resize');
          }
        }
      },

      grab(event) {
        this.preview.animation = false;
        this.rotating = this.preview.do.rotate(0, event.offsetX);
      },
      rotate(event) {
        if (this.rotating)
          this.rotating.next({ x: 0, y: event.offsetX });
      },
      release(event) {
        this.rotating.next();
        this.rotating = false;
        setTimeout(() => {
          this.preview.animation = this.animation;
        }, 1500);
      },
      show() {
        this.dialogVisible = true;
        if (!this.mirror) {
          this.$nextTick(() => {
            this.mirror = this.preview.mirror(this.$refs.max3d, {
              width: this.$refs.previewMax.clientWidth,
              height: this.$refs.previewMax.clientHeight,
            });
            this.mirror.render();
          });
        }
      },
      empty() {},
    },
    created() {
      document.body.addEventListener('keypress', (event => {
        if (this.onText && this.keyboard) {
          this.keyboard.next(event.key);
        }
      }).bind(this));
      document.body.addEventListener('keydown', (event => {
        if (this.onText && this.keyboard && event.key == 'Backspace') {
          this.keyboard.next(event.key);
        } else if (this.onText && this.keyboard && event.key == 'Enter') {
          this.drawer.focus(0);
          this.selected = 0;
          this.changeMode('resize');
        } else if (this.selected != -1 && event.key == 'Delete') {
          this.drawer.remove(this.selected);
          this.selected = -1;
        }
      }).bind(this));
    },
    mounted() {
      this.drawer = new Drawer(this.$refs.d2, {
        width: this.$refs.drawer.clientWidth,
        height: this.$refs.drawer.clientHeight,
        scale: 2
      });
      this.hover = this.drawer.helpers.hover();
      this.preview = new Preview(this.$refs.mini3d, {
        path: '../assets/models/cup.json',
        width: this.$refs.previewMini.clientWidth,
        height: this.$refs.previewMini.clientHeight,
        sceneColor: this.sceneColor,
        animation: false
      });
      this.preview.render();
    }
  }
  
 
</script>


<style>
  * {
    box-sizing: border-box;
  }
  html,
  body {
    height: 100%;
    margin: 0;
    background-color: #e8e8e8;
  }
  body {
    padding: 0 5px 5px;
  }
  #app {
    height: 100%;
  }
  #app > * {
    padding: 2.5px 0;
  }
  .column {
    display: flex;
    flex-direction: column;
  }
  .is-fullscreen.column .el-dialog__body {
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    align-items: stretch;
  }
  .stretch {
    display: flex;
    height: 100%;
    align-items: stretch;
  }
  .grow {
    flex-grow: 1
  }
  .el-col.stretch {
    display: flex;
    flex-direction: column;
  }
  .tool .title {
    font-family: Arial;
    font-weight: 500;
    font-size: 14px;
    text-transform: capitalize;
    color: #409EFF;
  }
  .tool.horizontal {
    display: flex;
    align-items: center;
    height: 40px;
    z-index: -1;
  }
  .tool.horizontal > .block.title{
    display: flex;
    align-items: center;
  }
  .space.around {
    border: none;
    background: #e8e8e8;
    justify-content: space-around;
  }
  .tool.vertical {
    display: flex;
    flex-direction: row;
    background-color: #fff;
    width: 37.7778em;
    border-bottom: 1px solid #555555;
  }
  .tool.vertical > * {
    margin: 0;
  }
  .el-dropdown-menu__item.selected {
    background-color: #D1E7FE !important;
  }
  .name {
    white-space: nowrap;
    font-family: Open Sans,Arial,sans-serif;
    font-weight: 400;
    font-size: 14px;
    padding-right: 15px;
    text-overflow: ellipsis;
    overflow: hidden;
    width: 168px;
  }
  .el-color-picker__trigger {
    border: none;
  }
  .el-radio__inner{
    background: blue;
  }
  #upload>.el-upload,
  #upload>.el-upload>.el-upload-dragger {
    width: 100%;
  }
  #upload>.el-upload>.el-upload-dragger {
    border-radius: 0;
    width: 100%;
    height: auto;
    padding: 4px;
  }
  #upload .el-upload-dragger .el-icon-upload {
    font-size: 25px;
    margin: 0;
  }
  .el-upload-dragger {
    background-color: transparent !important;
    border: none !important;
  }
  #previewMini {
    border: 1px dashed transparent;
    height: 33%;
    cursor: pointer;
    box-sizing: content;
  }
  #previewMax {
    flex-grow: 1;
  }
  #drawer {
    height: 37.6667em;
    width: 37.7778em;
    background: #acacac;
  }
  #d2 {
    width: 100%;
    height: 100%;
  }
  .el-upload-list {
    overflow-y: auto;
    overflow-x: hidden;
    height: 326px
  
  }
  .el-upload-list--picture .el-upload-list__item {
    border-radius: 0;
    border: 1px solid #fff;
    margin: 0;
    padding: 5px 5px 5px 85px;
    height: 65px;
    line-height: 1.3;
  }
  li.el-upload-list__item:hover{
    border: 1px solid #ff9900;
  }
  .el-upload-list--picture .el-upload-list__item.selected {
    background-color: #D1E7FE;
    border: 1px solid #336699;
  }
  .flip-list-move {
    transition: transform .5s;
  }
  *::-webkit-scrollbar{
    width: 10px;
    background-color: white;
  }
  *::-webkit-scrollbar-thumb{
    border-radius: 10px;
    background-color: rgba(0,0,0,.3);
  }
  .fix_button {
    margin: 5px 10px 10px 13px !important;
    background: #4b6891;
  }
  #fix-fs-button{
    font-size: 14px;
  }
  span#fix-upload{
    background-color: #fff;
    padding: 9px;
    margin-right: 8px;
  }
  .el-button--text {
    background-color: #4b6891;
    padding: 14px;
    color: #fff;
  }
  .fa-paint-brush:before {
    color: #3297ff; 
    background: #fff;
    padding: 3px;
    margin-right: 2px;
  }
  .fa {
     padding-right: 4px;
  }
  .el-button {
    font-size: 16px;
    border-radius: none;
  }
  i.figures:before {
    content: " ";
    background: url(../assets/img/figures.png) 60% 42% no-repeat #fff;
    padding: 5px 12px;
    margin-right: 6px;
  }
  .el-upload-dragger .el-icon-upload {
    line-height: 0;
  }
  .el-icon-upload:before{
    position: relative;
    top: 4px;
  }
  .el-upload-dragger:after{
    content: "Загрузить";
    font-size: 16px;
  }
  #fix-fs-button {
    padding: 10px;
  }
  .stretch.el-col.el-col-18 {
    width: inherit !important;
  }
.el-row--flex {
    display:block;
}
#mini3d {
    height: 240px !important;
    width: 266px !important;
    padding: 10px 2px 0 2px;
    margin-bottom: -2px;
}
.el-button+.el-button {
    margin-left: 0 !important;
}
.column.el-col.el-col-6 {
    margin-top: 4.5%;
}
.fix-3d_tool {
    border: 2px solid #336699;
    border-radius: 5px;
}
.div#app {
    display: flex;
    justify-content: center;
}
.wrapp {
    display: flex;
    justify-content: center;
}
.el-col-6 {
    width: 31% !important;
}
.download {
    margin: 2% 0 -3% 21%;
    display: block;
    width: 70%;
    border: 2px solid #336699;
    border-radius: 10px;
    background: #fff;
}
.fix__btn_downdload {
    background-color: #336699;
    display: inline-block;
    margin: 3px 50px 3px 220px;
}
.btn_download__text {
    font-family: Arial, sans-serif;
    font-size: 18px;
    font-weight: 500;
    display: inline-block;
    margin: 0 0 0 45px;
}
.btn_downdload {
    padding: 13px 25px 13px 25px;
}
.fix-button__mini {
    border-radius: 0px;
    font-size: 13px;
    padding: 5px;
    background: #4b6891 !important;
}
.el-upload-list--picture .el-upload-list__item-thumbnail {
    margin-top: 4px;
    width: 41px;
    height: 46px;
}
.button--movingLayers {
    font-size: 12px;
    padding: 2px;
    line-height: 1px;
    background: none;
    margin-left: 55px;
}
i.fa.fa-chevron-up {
    color:#b1b6c6;
}
i.fa.fa-chevron-down{
    color: #6ec1ff;
}
.btn-help {
    margin: 6px 0 5px 0;
}
.button--trigger {
    padding: 5px 4px 4px 4px;
    font-size: 16px;
    background-color: #4b6891 !important;
    border-radius: 0px;
}
.button--trigger:hover .fa-undo{
    color: #3e3b45;
    transition: .2s;
}
.fa-undo {
    padding: 4px;
    font-size: 20px;
    background-color: #fff;
    color: #c9c9c8;
    margin-right: 2px;
}
button.el-button.button--trigger.el-button--text:hover .fa-undo {
    color: 3e3b45;
}
.fa-undo__transform {
    transform: scale(-1, 1);
}
.line_y {
    padding: 12px 0 8px 0;
    margin: 0 5px 0 2px;
    border-right: 2px solid #c8c8c8;
}
.grid {
    display: inline-block;
    background: #fff;
    padding: 10px 6px 7px 0;
}
#grid {
    position: relative;
    bottom: 2px;
    color: #336699;
    font-size: 17px;
    padding: 3px;
}
.el-upload-list__item .el-icon-close {
   position: relative;
   top: 0;
   display: inline-block;
}
i.el-icon-close,
i.fa.fa-eye {
    background: #f2f2f2;
    padding: 8px;
    opacity: 1 !important;
}
.el-icon-close:before {
    color: #c8c8c8;
}
.el-upload-list--picture .el-upload-list__item.selected .el-icon-close:before {
     color: #212128;
}
.el-upload-list--picture .el-upload-list__item .fa-eye, 
.el-icon-close{
    color: #c8c8c8;
}
.el-upload-list--picture .el-upload-list__item.selected .fa-eye, 
.el-icon-close {
    color: #212128;
}
.text-blue {
    color: #46719f;
}
.text__user {
    font-size: 18px;
    font-family: Arial;
    text-align: center;
    padding: 9px 0;
    margin: 0;
}
.rules {
    background: #fff;
    margin-top: 34px;
    position: relative;
    padding-bottom: 15px;
}
.designations {
    font-size: 18px;
    font-family: Arial, sans-serif;
    font-weight: bold;
    padding: 5px 20px;
}
.rules__line {
    margin-bottom: 30px;
    opacity: 0.7;
    width: 85%;
}
.rules-designations--text {
    font-family: Arial, sans-serif;
    font-size: 14px;
    font-weight: 500;
    padding-left: 20px;
}
.rules__print {
    line-height: 0.2;
}
.green__line {
    border-bottom: 3px dashed #017800;
    position: absolute;
    left: 170px;
    bottom: 73px;
    width: 80px;
}
.red__line {
    border-bottom: 2px solid #ff0000;
    position: absolute;
    left: 170px;
    bottom: 33px;
    width: 80px;
}
.tool__user {
    background: #fff;
    padding: 0 15px;
    height: 190px;
    position: relative;
    margin-bottom: 30px;
}
.tool__user__border {
    padding: 65px 0 85px 0;
    margin: 0px 6px 0 6px;
    border-right: 1px solid #ccc;
    position: relative;
    top: 60px;
}
.el-input-number--mini {
    width: 105px;
}
.user__zoom::-webkit-inner-spin-button { 
  opacity: 1;
  padding: 3px;
  background-color: #fff !important;
  color: #0a0a0a;
  height: 25px;
  margin: 0;
}
.user__zoom {
    padding: 5px 0;
    border: 2px solid #cacaca;
    box-shadow: 1px 2px 2px #f3f3f3;
    text-align: center;
    margin-top: 5px;
}
.user__zoom__text {
    font-family: Arial, sans-serif;
    font-size: 16px;
    display: inline-block;
}
.tool__user__zoom {
    display: inline-block;
    position: absolute;
    top: 130px;
    left: 12px;
}
.user__rotate{
    display: inline-block;
    display: inline-block;
    position: absolute;
    left: 17px;
    font-size: 27px;
    top: 48px;
}
.user__zoom__text-rotate {
    display: inline-block;
    position: absolute;
    top: 90px;
    left: 15px;
    font-size: 16px;
    font-family: Arial;
}
.el-input__inner {
    background: #fff !important;
}

.el-select {
    margin-top: 15px;
    width: 26%;
}
.el-dropdown-menu {
    display: inline-block;
    margin: 0;
    padding: 0;
    box-shadow: none;
    top: 68px;
    left: 96px;
}
.fix__display {
    display: inline-block !important;
}
.container_dropdown_menu {
    display: inline-block;
}
.el-dropdown-menu {
    border: none;
}
.el-dropdown-menu__item {
    float: left;
    padding: 0 7px;
    font-size: 16px;
    color: #5a5e66;
}
.container-input-number {
    display: inline-block;
    position: absolute;
    left: 92px;
    top: 70%;
}
.AV {
    display: inline-block;
    margin: 0;
    line-height: 1;
    border-bottom: 2px solid #a7a7a7;
}
.big__T {
    display: inline-block;
    font-size: 26px;
    position: relative;
    top: 5px;
    margin: 0;
    font-family: Arial;
    font-weight: bold;
}
.small__T {
    display: inline-block;
    font-size: 13px;
    margin: 0;
    font-family: Arial;
    position: relative;
    top: 5px;
    left: 5px;
    font-weight: bold;
}
.el-color-dropdown__main-wrapper {
    display: flex;
    position: absolute;
    width: 70%;
}
.el-color-hue-slider.is-vertical {
    position: relative;
    left: 207px;
}
.el-color-picker__panel {
    padding: 0;
    display: block !important;
    border: 0;
    box-shadow: none;
}
.el-color-dropdown__btns {
    display: block;
}
.color-user--button {
    display: inline-block;
    float: right;
    margin-right: -12px;
}
.blocking, .el-color-picker--mini .el-color-picker__trigger {
    position: absolute;
    height: 23px;
    width: 55px;
    left: 275px;
    top: 40px;
    padding: 0;
    border: 1px solid #cccccc;
    border-radius: 0;
}
.blocking {
    position: absolute;
    z-index: 55;
    left: 547px;
    top: 45px;
}
.el-color-picker__icon.el-icon-arrow-down {
    display: none;
}

.color_text__button{
    position: relative;
    bottom: 2px;
    right: 8px;
    font-size: 14px;
    font-family: Arial, sans-serif;
    font-weight: 500;
}
.el-color-picker__color {
    border: 0;
    border-radius: 0;
}
.transperant__block {
    position: absolute;
    top: 67px;
    right: 7px;
    height: 23px;
    width: 55px; 
    padding: 0;
    border: 1px solid #cccccc;
    background: url(../assets/img/transparent.png);
}
.color_text__button__old {
    position: relative;
    top: 19px;
    left: -62px;
    font-size: 14px;
    font-family: Arial, sans-serif;
    font-weight: 500;
}
.button__success {
    display: block;
    position: relative;
    top: 50px;
    left: 15px;
}
.is-plain span {
    text-transform: lowercase;
}
.transparent__span {
    background-color: black;
    height: 20px;
}
.el-input--mini {
    display: none;
}
.el-color-dropdown__link-btn {
    display: none;
}
button.el-button.el-color-dropdown__btn.el-button--default.el-button--mini.is-plain {
    position: absolute;
    top: 140px;
    right: -30px;
    padding: 5px;
    border: 2px solid #336699;
    border-radius: 5px;
}
.transparent__background {
    position: absolute;
    top: -14px;
    left: 52px;
    padding: 13px;
    margin: 5p;
    background: url(../assets/img/transparent2.png) no-repeat 50% 70%;
    border: 2px solid #46c3e0;
}
.color_text__transparent {
    position: relative;
    top: -7px;
    left: -30px;
    font-size: 14px;
    font-family: Arial, sans-serif;
    font-weight: 500;
}

</style>
