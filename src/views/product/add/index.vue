<template>
  <div class="app-container">
    <el-page-header @back="goBack" content="新增产品"></el-page-header>
    <div class="article_add">
      <el-form
        ref="formRef"
        :v-model="productForm"
        :rules="rules"
        label-width="80px"
        label-position="left"
        style="margin-top: 20px"
      >
        <el-row>
          <el-col :span="12">
            <el-form-item label="名称" prop="name">
              <el-input v-model="productForm.name" />
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-col :span="24">
            <el-form-item label="特点" prop="desc">
              <el-input
                v-model="productForm.desc"
                type="textarea"
                style="width: 100%"
                :rows="5"
              />
            </el-form-item>
          </el-col>
        </el-row>

        <!-- <el-row>
          <el-col :span="12">
            <el-form-item label="状态" prop="status">
              <el-select
                v-model="productForm.status"
                placeholder="请选择文章状态"
              >
                <el-option
                  v-for="(item, index) in statusList"
                  :key="index"
                  :label="item.label"
                  :value="item.value"
                />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row> -->

        <el-row>
          <el-col :span="12">
            <el-form-item label="分类" prop="categoryId">
              <el-select
                v-model="productForm.categoryId"
                placeholder="请选择产品分类"
              >
                <el-option
                  v-for="(item, index) in categoryOptions"
                  :key="index"
                  :label="item.categoryName"
                  :value="item.categoryId"
                />
              </el-select>
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-col :span="12">
            <el-form-item label="排序" prop="orderId">
              <el-input
                type="number"
                v-model.number="productForm.orderId"
                placeholder="请填写排序id"
              />
            </el-form-item>
          </el-col>
        </el-row>

        <el-row>
          <el-col :span="12">
            <el-form-item label="封面" prop="coverImg">
              <el-upload
                class="avatar-uploader"
                action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
                list-type="picture-card"
                :show-file-list="false"
                :on-success="handleAvatarSuccess"
                :before-upload="beforeAvatarUpload"
              >
                <img v-if="productForm.coverImg" :src="productForm.coverImg" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-form-item>
          </el-col>
        </el-row>

        <!-- <el-row>
          <el-col :span="24">
            <el-form-item label="轮播图片" prop="bannerList">
              <el-upload
                class="avatar-uploader"
                action="https://run.mocky.io/v3/9d059bf9-4660-45f2-925d-ce80ad6c4d15"
                list-type="picture-card"
                :show-file-list="false"
                :on-preview="handlePictureCardPreview"
                :on-remove="handleRemove"
              >
                <img v-if="imageUrl" :src="imageUrl" class="avatar" />
                <i v-else class="el-icon-plus avatar-uploader-icon"></i>
              </el-upload>
            </el-form-item>
          </el-col>
        </el-row> -->

        <!-- <el-row>
          <el-col
            :span="11"
            :style="{ paddingLeft: '60px', marginBottom: '20px' }"
          >
            <img
              v-if="showCoverImg"
              :src="productForm.coverImg"
              class="cover"
            />
          </el-col>
        </el-row> -->

        <el-row> 产品参数 </el-row>
        <br />

        <el-row v-for="(item, index) in productForm.specs" :key="item + index">
          <el-col :span="11">
            <el-form-item
              label="属性"
              :prop="'specs.' + index + '.key'"
              :rules="[
                {
                  required: true,
                  message: '请输入属性',
                  trigger: 'blur',
                },
              ]"
            >
              <el-input v-model="item.key" placeholder="请输入属性" />
            </el-form-item>
          </el-col>
          <el-col :span="11">
            <el-form-item
              label="值"
              :prop="'specs.' + index + '.value'"
              :rules="[
                {
                  required: true,
                  message: '请输入属性值',
                  trigger: 'blur',
                },
              ]"
            >
              <el-input v-model="item.value" placeholder="请输入值" />
            </el-form-item>
          </el-col>
          <el-col :span="2">
            <el-button
              type="text"
              @click="handleDelSpecs(index)"
              v-show="productForm.specs.length > 1"
              >删除
            </el-button>
          </el-col>
        </el-row>

        <el-row>
          <el-col :span="12">
            <el-form-item>
              <el-button @click="handleAddSpecs"> 添加一行 </el-button>
            </el-form-item>
          </el-col>
        </el-row>

        <br />

        <el-row>
          <el-col :span="24">
            <el-form-item>
              <el-button type="primary" @click="submit"> 提交 </el-button>
            </el-form-item>
          </el-col>
        </el-row>
      </el-form>
    </div>
  </div>
</template>

<script>
import { addProduct, updateProduct } from "@/api/product/manage";
import { listCategoryOptions } from "@/api/product/category";

export default {
  name: "AddProduct",
  data() {
    return {
      productForm: {
        name: "",
        desc: "",
        categoryId: "",
        status: "",
        orderId: "",
        coverImg: "",
        bannerList: [],
        specs: [{ key: "", value: "" }],
      },
      rules: {
        name: [{ required: true, message: "请输入产品名称", trigger: "blur" }],
        desc: [{ required: true, message: "请输入产品特点", trigger: "blur" }],
        // coverImg: [
        //     {
        //         required: true,
        //         message: "请使用一张图片作为封面",
        //         trigger: "blur",
        //     },
        // ],
        // bannerList:[
        //     { required: true, message: "请添加产品轮播图", trigger: "change" }
        // ],
        categoryId: [
          { required: true, message: "请选择产品分类", trigger: "change" },
        ],
        orderId: [
          { required: true, message: "请选择产品排序", trigger: "change" },
        ],
        status: [{ required: true, message: "请选择状态", trigger: "change" }],
      },
      imageUrl: "",
      categoryOptions:[]
    };
  },
  created(){
    this.getCategory()
  },
  methods: {
    async getCategory() {
      try {
        const { code, rows } = await listCategoryOptions();
        if (code === 200) {
          this.categoryOptions = rows;
        }
      } catch (err) {
        console.log(err);
      }
    },
    goBack() {
      this.$router.push("/product/manage");
    },

    handleAvatarSuccess(response, uploadFile) {
    //    = URL.createObjectURL(uploadFile.raw);
      this.productForm.coverImg = res.data[0].url;
    },
    beforeAvatarUpload(rawFile) {
      if (rawFile.type !== "image/jpeg") {
        this.$message.error('Avatar picture must be JPG format!')
        return false;
      } else if (rawFile.size / 1024 / 1024 > 2) {
        this.$message.error("Avatar picture size can not exceed 2MB!")
        return false;
      }
      return true;
    },
    handleAddSpecs() {
      this.productForm.specs.push({ key: "", vlaue: "" });
    },
    handleDelSpecs(index) {
      this.productForm.specs.splice(index, 1);
    },
    submit(){

    }
  },
};
</script>

<style lang="scss" scoped>
.article_add {
  background: #fff;
  padding: 15px;
}

// .avatar-uploader .el-upload {
//     border: 1px dashed #d9d9d9;
//     border-radius: 6px;
//     cursor: pointer;
//     position: relative;
//     overflow: hidden;
//   }
//   .avatar-uploader .el-upload:hover {
//     border-color: #409EFF;
//   }
//   .avatar-uploader-icon {
//     font-size: 28px;
//     color: #8c939d;
//     width: 178px;
//     height: 178px;
//     line-height: 178px;
//     text-align: center;
//   }
//   .avatar {
//     width: 178px;
//     height: 178px;
//     display: block;
//   }

.cus-btn {
  width: 100%;
  border: 1px dashed gray;
}

.cover {
  width: 50%;
  border: 1px dashed #ccc;
  border-radius: 5px;
  margin-left: 20px;
}

.el-input,
.el-select {
  width: 80% !important;
}

:deep input[type="number"]::-webkit-inner-spin-button,
:deep input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none !important;
}
</style>
