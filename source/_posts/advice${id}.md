### /advice/${id}

1. 非会诊/非调整解决方案，调整达标值：

   - status为[ 0 , 5 ] , api.patch(`advice/${id}`, {category: 6, status: 下级医生编辑了达标值，上级医生没有编辑；

     并且 上级医生想修改，即改回原来的达标值，status为10，其他为5})；  

   - status非[ 0 , 5 ]，api.post('advice'，{ category: 6, status: 无 })；

   - 忽略： 下级医生 或者 没有下级医生的上级医生 api.patch(`advice/${id}`, { category: 6, 上级医生status为2，下级为6});

2. 调整用药

   - 主动调药，api.post('advice'，{ category: 无, status: 无 })；
   - 非主动调药  api.patch(`advice/${id}`, {category: 6, status: 1-> 同意, 2->医生忽略 5-> 医助同意 6-> 医助忽略})；
   - 忽略：api.patch(`advice/${id}`, {category: 1,3,4,5, status:  下级医生为6，上级为2});

3. 调整治疗方案

   - 会诊医生审核，  api.post('advice'，{ category:11, status: 1-> 同意, 2->医生忽略  })；

4. 提醒医生

   -  api.patch(`advice/${id}`, { 忽略 -> category: 2, status: 2 ; 同意-> category: 0, status: 1; 修改-> category: 0, status: 3; 知道了-> category: 7,  status: 1;  }); 

5. 会诊

   - 会诊医生， api.post(`advice/${id}`, { categoey: 无或者11，status: "CONSULTATION_OPINION"  }); 

   - 上级医生审核， api.patch(`advice/${id}`, {  category：无或者11， status: "RESPONSE" }); 