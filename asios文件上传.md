```
//文件上传
    uploadOpean() {
      this.addTeamLayer = true;
    },
    //文件移除
    handleRemove(file, fileList) {
      console.log(file, "958");
    },
    //上传的文件错误
    uploadError(res, file, fileList) {
      this.$message({
        type: "error",
        message: "上传失败！请重试"
      });
    },
    //文件上传成功
    uploadSuccess(res, file, fileList) {
      console.log("上传文件", res, file, fileList);
    },
    // 上传前对文件的大小的判断
    beforeAvatarUpload(file) {
      const extension = file.name.split(".")[1] === "xls";
      const extension2 = file.name.split(".")[1] === "xlsx";
      const extension3 = file.name.split(".")[1] === "doc";
      const extension4 = file.name.split(".")[1] === "docx";
      const fileSize = file.size / 1024 / 1024 < 20;
      if (!extension && !extension2 && !extension3 && !extension4) {
        this.$message({
          type: "error",
          message: "上传模板只能是 xls、xlsx、doc、docx 格式!"
        });
      }
      if (!fileSize) {
        this.$message({
          type: "error",
          message: "上传模板大小不能超过 20MB!"
        });
      }
      return extension || extension2 || extension3 || (extension4 && fileSize);
    },
    //上传文件确认
    uploadSubmit() {
      if (this.$refs.upload.uploadFiles.length > 0) {
        this.$refs.upload.submit();
        this.addTeamLayer = false;
        this.borwoseList = [];
        this.borwose_count = 0;
      } else {
        this.$message({
          type: "error",
          message: "请选择文件！"
        });
      }
    },
    //打开并预览
    opeanBrowse() {
      if (this.$refs.upload.uploadFiles.length > 0) {
        const self = this;
        let file = this.$refs.upload.$el.children[0].children[1].files[0];
        let fileUpload = new FormData();
        fileUpload.append("file", file);
        fileUpload.append(
          "staffId",
          JSON.parse(window.sessionStorage.getItem("staff")).staffId
        );
        fileUpload.append("source", this.GlobalVal.constants.source);
        fileUpload.append("limit", this.borwose_limit);
        fileUpload.append("page", this.borwose_page);
        let config = {
          headers: { "Content-Type": "multipart/form-data" }
        };
        this.loading = true;
        this.$axios
          .post(this.GlobalVal.httpLink.viewClassExclFile, fileUpload, config)
          .then(
            res => {
              if (res.data.code === "10000") {
                this.$message({
                  type: "success",
                  message: "预览成功!"
                });
                this.loading = false;
                this.borwose_count = res.data.data.count;
                this.borwoseList = res.data.data.classes;
                this.borwoseList = this.borwoseList.map(item => {
                  item.subject = self.Util.GetLabelForValue(
                    self.$store.state.SubjectOptions,
                    item.subject
                  );
                  item.season = self.Util.GetLabelForValue(
                    self.$store.state.SeasonOptions,
                    item.season
                  );
                  item.periods = self.Util.GetLabelForValue(
                    self.$store.state.PeriodsOptions,
                    item.periods
                  );
                  item.grade = self.Util.GetLabelForValue(
                    self.$store.state.GradeOptions,
                    item.grade
                  );
                  item.project_type = self.Util.GetLabelForValue(
                    self.$store.state.ProjectTypeOptions,
                    item.project_type
                  );
                  item.class_type = self.Util.GetLabelForValue(
                    self.$store.state.ClassTypeOptions,
                    item.class_type
                  );
                  item.liberal_type = self.Util.GetLabelForValue(
                    self.$store.state.LiberalTypeOptions,
                    item.liberal_type
                  );
                  item.turn_class_condition = self.Util.GetLabelForValue(
                    self.$store.state.TurnClassConditionOptions,
                    item.turn_class_condition
                  );
                  item.charge_generation =
                    item.charge_generation == false ? "否" : "是";
                  return item;
                });
              } else {
                this.$message({
                  type: "error",
                  message: res.data.errmsg
                });
              }
            },
            res => {
              console.error(res);
              this.loading = false;
              this.$message({
                type: "error",
                message: "操作失败"
              });
            }
          );
      } else {
        this.$message({
          type: "error",
          message: "请选择文件！"
        });
      }
    },
    //关闭上传文件
    uploadClose() {
      this.borwoseList = [];
      this.borwose_count = 0;
      this.addTeamLayer = false;
    }
```

