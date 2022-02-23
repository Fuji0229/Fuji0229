## Day Planner
- [ ] public enum ConsumableMatchStatus {  
	按照耗材编码或院内收费编码未查询到contractdetail表内对应信息
    UN_MATCH_CHARGE_CODE("未匹配到院内收费编码"),  
	
	根据就诊科室编码未查到departmentToHis表中对应的科室信息
	if (departmentToHisResponses == null || departmentToHisResponses.isEmpty()) 
    UN_MATCH_DEPARTMENT("未匹配到科室"),  

	暂未使用
    UN_MATCH_ITEM_NO("未匹配到物品编码"),  

	暂未使用
    UN_MATCH_HIGH_VALUE("未匹配到高值唯一编码"),  
	
	暂未使用
    UN_MATCH_UDI("未匹配到UDI"),  

	单据中至少有一个耗材的库存（materialInfo）大于当前消耗的数量
    INVENTORY_IS_NOT_ENOUGH("计量大于出库"),  

	
	NORMAL("正常计费"),  

	保存失败
    MATERIAL_FAIL("生成科室消耗单失败");  
  
    private String name;  
}