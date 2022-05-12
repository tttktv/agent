var GLOBAL = {}
GLOBAL["CC_ZN_RESUMES"] = {
	is_update: tools_web.is_true(Param.IS_RES_UPDATE),
	total: 0,
	updated: 0
}

// Проверка наличия объекта в базе.
function HasWTObject(sTable, sField, sValue)
{
	try {

		//return ArrayOptFirstElem(XQuery("sql: SELECT id FROM " + sTable + " WHERE " + sField + " = " + XQueryLiteral(sValue) + "")) != undefined
return ArrayOptFirstElem(XQuery("for $elem in "+sTable+" where $elem/"+sField+"='"+sValue+"' return $elem")) != undefined

	} catch(e) {
		throw Error("HasWTObject -> " + e.message)
	}
}

function ZnRequestAPI(method, query)
{
	try {
		var auth = Base64Encode("wt_exchange:ml9-bukin")
		var res = HttpRequest("https://15-vs-zupapp02.nestro.ru/zup_etalon/hs/employeesinfo" + method + "?" + query, 'post', null, "Authorization: Basic " + auth + "\nAsk-Auth:0\nContent-Type: application/json\n")

		var code = res.RespCode

		if(code != 200)
		     throw Error("ZnRequestAPI -> Сервер возвратил ошибку: " + code)

		// Логирование ответа на запрос.
		if(method == "COLLABORATORLIST" && GLOBAL["COLLABORATORS"].GetOptProperty("is_log_request", false))
			Log(res.Body)


		return tools.read_object(res.Body)

	} catch(e) {
		throw Error("ZnRequestAPI -> " + e.message)
	}
}




// Заполнение справки.
function fillResume(teRes){
	//teRes.col_code = oRes["Collaborator"].code //Код
	teRes.grade = oRes["ОбщаяИнформация"].GetOptProperty("Грейд", "") //Грейд
	teRes.prof_status = oRes["ОбщаяИнформация"].GetOptProperty("ПрофессиональныйСтатус", "") //ПрофессиональныйСтатус
	
	if(oRes["ОбщаяИнформация"].HasProperty("ОбразованиеОсновное")){ //ОбразованиеОсновное
		if(ArrayCount(teRes.base_edus)!=0){
			teRes.base_edus.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("ОбразованиеОсновное", "").length;i++){
			base_ed = teRes.base_edus.AddChild();
			base_ed.name = oRes["ОбщаяИнформация"].GetOptProperty("ОбразованиеОсновное", "")[i].GetOptProperty("Название", "");
		}
	}

	if(oRes["ОбщаяИнформация"].HasProperty("ОбразованиеДополнительное")){ //ОбразованиеДополнительное
		if(ArrayCount(teRes.additional_edus)!=0){
			teRes.additional_edus.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("ОбразованиеДополнительное", "").length;i++){
			add_ed = teRes.additional_edus.AddChild();
			add_ed.name = oRes["ОбщаяИнформация"].GetOptProperty("ОбразованиеДополнительное", "")[i].GetOptProperty("Название", "");
		}
	}

	if(oRes["ОбщаяИнформация"].HasProperty("УченаяСтепень")){ //УченаяСтепень
		if(ArrayCount(teRes.academic_degrees)!=0){
			teRes.academic_degrees.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("УченаяСтепень", "").length;i++){
			ac_de = teRes.academic_degrees.AddChild();
			ac_de.name = oRes["ОбщаяИнформация"].GetOptProperty("УченаяСтепень", "")[i].GetOptProperty("Название", "");
		}
	}


	if(oRes["ОбщаяИнформация"].HasProperty("ЗнаниеИностранногоЯзыка")){ //ЗнаниеИностранногоЯзыка
		if(ArrayCount(teRes.languages)!=0){
			teRes.languages.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("ЗнаниеИностранногоЯзыка", "").length;i++){
			lan = teRes.languages.AddChild();
			lan.name = oRes["ОбщаяИнформация"].GetOptProperty("ЗнаниеИностранногоЯзыка", "")[i].GetOptProperty("Язык", "");
			lan.level = oRes["ОбщаяИнформация"].GetOptProperty("ЗнаниеИностранногоЯзыка", "")[i].GetOptProperty("УровеньВладения", "");
		}
	}

	if(oRes["ОбщаяИнформация"].HasProperty("СтажРаботыОбщий")){  //СтажРаботыОбщий
		teRes.work_exp_gen = oRes["ОбщаяИнформация"].GetOptProperty("СтажРаботыОбщий", "")
	}
	if(oRes["ОбщаяИнформация"].HasProperty("СтажРаботыОтраслевой")){  //СтажРаботыОтраслевой
		teRes.work_exp_ind = oRes["ОбщаяИнформация"].GetOptProperty("СтажРаботыОтраслевой", "")
	}
	if(oRes["ОбщаяИнформация"].HasProperty("МестаРаботыЗаПоследние10Лет")){ //МестаРаботыЗаПоследние10Лет
		if(ArrayCount(teRes.work_exp_specs)!=0){
			teRes.work_exp_specs.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("МестаРаботыЗаПоследние10Лет", "").length;i++){
			exp = teRes.work_exp_specs.AddChild();
			exp.name = oRes["ОбщаяИнформация"].GetOptProperty("МестаРаботыЗаПоследние10Лет", "")[i].GetOptProperty("Название", "");
			exp.start_date = oRes["ОбщаяИнформация"].GetOptProperty("МестаРаботыЗаПоследние10Лет", "")[i].GetOptProperty("Начало", "");
			exp.finish_date = oRes["ОбщаяИнформация"].GetOptProperty("МестаРаботыЗаПоследние10Лет", "")[i].GetOptProperty("Окончание", "");
			exp.total = oRes["ОбщаяИнформация"].GetOptProperty("МестаРаботыЗаПоследние10Лет", "")[i].GetOptProperty("Всего", "");
		}
	}

	if(oRes["ОбщаяИнформация"].HasProperty("ОпытРаботыЗаРубежом")){ //ОпытРаботыЗаРубежом
		if(ArrayCount(teRes.work_exp_adroads)!=0){
			teRes.work_exp_adroads.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("ОпытРаботыЗаРубежом", "").length;i++){
			exp = teRes.work_exp_adroads.AddChild();
			exp.name = oRes["ОбщаяИнформация"].GetOptProperty("ОпытРаботыЗаРубежом", "")[i].GetOptProperty("Название", "");
			exp.start_date = oRes["ОбщаяИнформация"].GetOptProperty("ОпытРаботыЗаРубежом", "")[i].GetOptProperty("Начало", "");
			exp.finish_date = oRes["ОбщаяИнформация"].GetOptProperty("ОпытРаботыЗаРубежом", "")[i].GetOptProperty("Окончание", "");
			exp.total = oRes["ОбщаяИнформация"].GetOptProperty("ОпытРаботыЗаРубежом", "")[i].GetOptProperty("Всего", "");
		}
	}
	if(oRes["ОбщаяИнформация"].HasProperty("ОпытРаботыЗаРубежомВсего")){  //ОпытРаботыЗаРубежомВсего
		teRes.work_exp_adroad_total = oRes["ОбщаяИнформация"].GetOptProperty("ОпытРаботыЗаРубежомВсего", "");
	}

	if(oRes["ОбщаяИнформация"].HasProperty("Награды")){ //Награды
		if(ArrayCount(teRes.awards)!=0){
			teRes.awards.Clear();
		}
		for(i=0;i<oRes["ОбщаяИнформация"].GetOptProperty("Награды", "").length;i++){
			aw = teRes.awards.AddChild();
			aw.year = oRes["ОбщаяИнформация"].GetOptProperty("Награды", "")[i].GetOptProperty("Год", "");
			aw.name = oRes["ОбщаяИнформация"].GetOptProperty("Награды", "")[i].GetOptProperty("Название", "");
			aw.value = oRes["ОбщаяИнформация"].GetOptProperty("Награды", "")[i].GetOptProperty("Значение", "");
		}
	}

	///////////////////ИнформацияРезультатахОценочныхПроцедур//////////////////////////
	if(oRes.HasProperty("ИнформацияРезультатахОценочныхПроцедур")){

			if(oRes["ИнформацияРезультатахОценочныхПроцедур"].HasProperty("АнализВербальнойИнформации")){ //АнализВербальнойИнформации
				if(ArrayCount(teRes.analysis_varbals)!=0){
					teRes.analysis_varbals.Clear();
				}
				for(i=0;i<oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("АнализВербальнойИнформации", "").length;i++){
					av = teRes.analysis_varbals.AddChild();
					av.year = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("АнализВербальнойИнформации", "")[i].GetOptProperty("Год", "");
					av.value = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("АнализВербальнойИнформации", "")[i].GetOptProperty("Оценка", "");
				}
			}

			if(oRes["ИнформацияРезультатахОценочныхПроцедур"].HasProperty("АнализЧисловойИнформации")){ //АнализЧисловойИнформации
				if(ArrayCount(teRes.analysis_numerics)!=0){
					teRes.analysis_numerics.Clear();
				}
				for(i=0;i<oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("АнализЧисловойИнформации", "").length;i++){
					an = teRes.analysis_numerics.AddChild();
					an.year = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("АнализЧисловойИнформации", "")[i].GetOptProperty("Год", "");
					an.value = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("АнализЧисловойИнформации", "")[i].GetOptProperty("Оценка", "");
				}
			}

			if(oRes["ИнформацияРезультатахОценочныхПроцедур"].HasProperty("ОценкаПотенциала")){ //ОценкаПотенциала
				if(ArrayCount(teRes.potentials)!=0){
					teRes.potentials.Clear();
				}
				for(i=0;i<oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ОценкаПотенциала", "").length;i++){
					p = teRes.potentials.AddChild();
					p.year = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ОценкаПотенциала", "")[i].GetOptProperty("Год", "");
					p.value = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ОценкаПотенциала", "")[i].GetOptProperty("Оценка", "");
				}
			}

			if(oRes["ИнформацияРезультатахОценочныхПроцедур"].HasProperty("ИтоговаяОценка")){ //ИтоговаяОценка
				if(ArrayCount(teRes.final_assessments)!=0){
					teRes.final_assessments.Clear();
				}
				for(i=0;i<oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ИтоговаяОценка", "").length;i++){
					fa = teRes.final_assessments.AddChild();
					fa.year = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ИтоговаяОценка", "")[i].GetOptProperty("Год", "");
					fa.name = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ИтоговаяОценка", "")[i].GetOptProperty("Название", "");
					fa.value = oRes["ИнформацияРезультатахОценочныхПроцедур"].GetOptProperty("ИтоговаяОценка", "")[i].GetOptProperty("Оценка", "");
				}
			}

	}

	///////////////////Результативность//////////////////////////
	if(oRes.HasProperty("Результативность")){

			if(oRes["Результативность"].HasProperty("ОценкаВыполненияКПЭ")){ //ОценкаВыполненияКПЭ
				if(ArrayCount(teRes.kpi_assessments)!=0){
					teRes.kpi_assessments.Clear();
				}
				for(i=0;i<oRes["Результативность"].GetOptProperty("ОценкаВыполненияКПЭ", "").length;i++){
					kp = teRes.kpi_assessments.AddChild();
					kp.year = oRes["Результативность"].GetOptProperty("ОценкаВыполненияКПЭ", "")[i].GetOptProperty("Год", "");
					kp.value = oRes["Результативность"].GetOptProperty("ОценкаВыполненияКПЭ", "")[i].GetOptProperty("Оценка", "");
				}
			}	
			if(oRes["Результативность"].HasProperty("ИтогиЭффективностиРаботника")){ //ИтогиЭффективностиРаботника
				if(ArrayCount(teRes.result_performances)!=0){
					teRes.result_performances.Clear();
				}
				for(i=0;i<oRes["Результативность"].GetOptProperty("ИтогиЭффективностиРаботника", "").length;i++){
					kp = teRes.result_performances.AddChild();
					kp.year = oRes["Результативность"].GetOptProperty("ИтогиЭффективностиРаботника", "")[i].GetOptProperty("Год", "");
					kp.value = oRes["Результативность"].GetOptProperty("ИтогиЭффективностиРаботника", "")[i].GetOptProperty("Оценка", "");
				}
			}	
			if(oRes["Результативность"].HasProperty("ИнформацияДисциплинарныхВзысканий")){ //ИнформацияДисциплинарныхВзысканий
				if(ArrayCount(teRes.disciplinary_infos)!=0){
					teRes.disciplinary_infos.Clear();
				}
				for(i=0;i<oRes["Результативность"].GetOptProperty("ИнформацияДисциплинарныхВзысканий", "").length;i++){
					kp = teRes.disciplinary_infos.AddChild();
					kp.year = oRes["Результативность"].GetOptProperty("ИнформацияДисциплинарныхВзысканий", "")[i].GetOptProperty("Год", "");
					kp.value = oRes["Результативность"].GetOptProperty("ИнформацияДисциплинарныхВзысканий", "")[i].GetOptProperty("Оценка", "");
				}
			}

	}

	///////////////////ПроектнаяДеятельность//////////////////////////
	if(oRes.HasProperty("ПроектнаяДеятельность")){

			if(oRes["ПроектнаяДеятельность"].HasProperty("ПроектыИзменений")){ //ПроектыИзменений
				if(ArrayCount(teRes.change_projects)!=0){
					teRes.change_projects.Clear();
				}
				for(i=0;i<oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыИзменений", "").length;i++){
					cp = teRes.change_projects.AddChild();
					cp.year = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыИзменений", "")[i].GetOptProperty("Год", "");
					cp.name = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыИзменений", "")[i].GetOptProperty("Название", "");
					cp.value = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыИзменений", "")[i].GetOptProperty("Описание", "");
					cp.status = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыИзменений", "")[i].GetOptProperty("СтатусПроекта", "");
				}
			}	
			if(oRes["ПроектнаяДеятельность"].HasProperty("ПроектыРазвитияБизнеса")){ //ПроектыРазвитияБизнеса
				if(ArrayCount(teRes.development_projects)!=0){
					teRes.development_projects.Clear();
				}
				for(i=0;i<oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыРазвитияБизнеса", "").length;i++){
					dp = teRes.development_projects.AddChild();
					dp.year = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыРазвитияБизнеса", "")[i].GetOptProperty("Год", "");
					dp.name = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыРазвитияБизнеса", "")[i].GetOptProperty("Название", "");
					dp.value = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыРазвитияБизнеса", "")[i].GetOptProperty("Описание", "");
					dp.status = oRes["ПроектнаяДеятельность"].GetOptProperty("ПроектыРазвитияБизнеса", "")[i].GetOptProperty("СтатусПроекта", "");
				}
			}	

	}

	///////////////////АктивнаяПозиция//////////////////////////
	if(oRes.HasProperty("АктивнаяПозиция")){

			if(oRes["АктивнаяПозиция"].HasProperty("ПредложенияЕстьИдея")){ //ПредложенияЕстьИдея
				if(ArrayCount(teRes.have_ideas)!=0){
					teRes.have_ideas.Clear();
				}
				for(i=0;i<oRes["АктивнаяПозиция"].GetOptProperty("ПредложенияЕстьИдея", "").length;i++){
					ha = teRes.have_ideas.AddChild();
					ha.year = oRes["АктивнаяПозиция"].GetOptProperty("ПредложенияЕстьИдея", "")[i].GetOptProperty("Год", "");
					ha.filed_value = oRes["АктивнаяПозиция"].GetOptProperty("ПредложенияЕстьИдея", "")[i].GetOptProperty("Подано", "");
					ha.introduced_value = oRes["АктивнаяПозиция"].GetOptProperty("ПредложенияЕстьИдея", "")[i].GetOptProperty("Внедрено", "");
				}
			}	
			if(oRes["АктивнаяПозиция"].HasProperty("ЯвляетсяЭкспертомКандидатом")){//ЯвляетсяЭкспертомКандидатом
				teRes.is_expert_company = oRes["АктивнаяПозиция"].GetOptProperty("ЯвляетсяЭкспертомКандидатом", "")
			}
			if(oRes["АктивнаяПозиция"].HasProperty("КатегорияОбластьЭксперта")){ //КатегорияОбластьЭксперта
				if(ArrayCount(teRes.exp_cat_fields)!=0){
					teRes.exp_cat_fields.Clear();
				}
				for(i=0;i<oRes["АктивнаяПозиция"].GetOptProperty("КатегорияОбластьЭксперта", "").length;i++){
					field = teRes.exp_cat_fields.AddChild();
					field.category = oRes["АктивнаяПозиция"].GetOptProperty("КатегорияОбластьЭксперта", "")[i].GetOptProperty("Категория", "");
					field.field = oRes["АктивнаяПозиция"].GetOptProperty("КатегорияОбластьЭксперта", "")[i].GetOptProperty("Область", "");
				}
			}

			if(oRes["АктивнаяПозиция"].HasProperty("ЯвляетсяМетодологом")){//ЯвляетсяМетодологом
				teRes.is_expert_methodologist = oRes["АктивнаяПозиция"].GetOptProperty("ЯвляетсяМетодологом", "") 
			}
			if(oRes["АктивнаяПозиция"].HasProperty("КатегорияОбластьМетодолога")){ //КатегорияОбластьМетодолога
				if(ArrayCount(teRes.met_cat_fields)!=0){
					teRes.met_cat_fields.Clear();
				}
				for(i=0;i<oRes["АктивнаяПозиция"].GetOptProperty("КатегорияОбластьМетодолога", "").length;i++){
					field = teRes.met_cat_fields.AddChild();
					field.category = oRes["АктивнаяПозиция"].GetOptProperty("КатегорияОбластьМетодолога", "")[i].GetOptProperty("Категория", "");
					field.field = oRes["АктивнаяПозиция"].GetOptProperty("КатегорияОбластьМетодолога", "")[i].GetOptProperty("Область", "");
				}
			}

			if(oRes["АктивнаяПозиция"].HasProperty("УчастиеКонкурсах")){ //УчастиеКонкурсах
				if(ArrayCount(teRes.info_competitions)!=0){
					teRes.info_competitions.Clear();
				}
				for(i=0;i<oRes["АктивнаяПозиция"].GetOptProperty("УчастиеКонкурсах", "").length;i++){
					comp = teRes.info_competitions.AddChild();
					comp.year = oRes["АктивнаяПозиция"].GetOptProperty("УчастиеКонкурсах", "")[i].GetOptProperty("Год", "");
					comp.name = oRes["АктивнаяПозиция"].GetOptProperty("УчастиеКонкурсах", "")[i].GetOptProperty("Название", "");
					comp.status = oRes["АктивнаяПозиция"].GetOptProperty("УчастиеКонкурсах", "")[i].GetOptProperty("Статус", "");
				}
			}

	}


	///////////////////ДополнительныеПоказатели//////////////////////////
	if(oRes.HasProperty("ДополнительныеПоказатели")){

			if(oRes["ДополнительныеПоказатели"].HasProperty("ЯвляетсяРаботникомКадровогоРезерва")){//ЯвляетсяРаботникомКадровогоРезерва
				teRes.is_info_reserve = oRes["ДополнительныеПоказатели"].GetOptProperty("ЯвляетсяРаботникомКадровогоРезерва", "") 
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("ИнформацияРаботникаКадровогоРезерва")){ //ИнформацияРаботникаКадровогоРезерва
				if(ArrayCount(teRes.inf_reserves)!=0){
					teRes.inf_reserves.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("ИнформацияРаботникаКадровогоРезерва", "").length;i++){
					ires = teRes.inf_reserves.AddChild();
					ires.category = oRes["ДополнительныеПоказатели"].GetOptProperty("ИнформацияРаботникаКадровогоРезерва", "")[i].GetOptProperty("Категория", "");
					ires.readiness = oRes["ДополнительныеПоказатели"].GetOptProperty("ИнформацияРаботникаКадровогоРезерва", "")[i].GetOptProperty("Степень готовности", "");
					ires.position = oRes["ДополнительныеПоказатели"].GetOptProperty("ИнформацияРаботникаКадровогоРезерва", "")[i].GetOptProperty("Позиция", "");
				}
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("ЯвляетсяУчастникомСистемыНаставничества")){//ЯвляетсяУчастникомСистемыНаставничества
				teRes.is_mentoring_system = oRes["ДополнительныеПоказатели"].GetOptProperty("ЯвляетсяУчастникомСистемыНаставничества", "") 
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("ИнформацияСистемыНаставничества")){//ИнформацияСистемыНаставничества
				teRes.mentoring_system = oRes["ДополнительныеПоказатели"].GetOptProperty("ИнформацияСистемыНаставничества", "") 
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("УчастиеВоВнешнихРейтингах")){ //УчастиеВоВнешнихРейтингах
				if(ArrayCount(teRes.external_ratings)!=0){
					teRes.external_ratings.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеВоВнешнихРейтингах", "").length;i++){
					rtng = teRes.external_ratings.AddChild();
					rtng.year = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеВоВнешнихРейтингах", "")[i].GetOptProperty("Год", "");
					rtng.name = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеВоВнешнихРейтингах", "")[i].GetOptProperty("Название", "");
					rtng.status = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеВоВнешнихРейтингах", "")[i].GetOptProperty("Статус", "");
				}
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("УчастиеПрочихКонкурсах")){ //УчастиеПрочихКонкурсах
				if(ArrayCount(teRes.other_comps)!=0){
					teRes.other_comps.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеПрочихКонкурсах", "").length;i++){
					comp = teRes.other_comps.AddChild();
					comp.year = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеПрочихКонкурсах", "")[i].GetOptProperty("Год", "");
					comp.name = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеПрочихКонкурсах", "")[i].GetOptProperty("Название", "");
					comp.status = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеПрочихКонкурсах", "")[i].GetOptProperty("Статус", "");
				}
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("УчастиеКонференциях")){ //УчастиеКонференциях
				if(ArrayCount(teRes.conference_speakers)!=0){
					teRes.conference_speakers.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКонференциях", "").length;i++){
					conf = teRes.conference_speakers.AddChild();
					conf.year = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКонференциях", "")[i].GetOptProperty("Год", "");
					conf.name = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКонференциях", "")[i].GetOptProperty("Название", "");
					conf.status = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКонференциях", "")[i].GetOptProperty("Статус", "");
				}
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("УчастиеМозговомШтурме")){ //УчастиеМозговомШтурме
				if(ArrayCount(teRes.brainstorming_dg_levels)!=0){
					teRes.brainstorming_dg_levels.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеМозговомШтурме", "").length;i++){
					br = teRes.brainstorming_dg_levels.AddChild();
					br.year = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеМозговомШтурме", "")[i].GetOptProperty("Год", "");
					br.name = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеМозговомШтурме", "")[i].GetOptProperty("Название", "");
				}
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("УчастиеКорпоративныхМероприятиях")){ //УчастиеКорпоративныхМероприятиях
				if(ArrayCount(teRes.corporate_events)!=0){
					teRes.corporate_events.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКорпоративныхМероприятиях", "").length;i++){
					event = teRes.corporate_events.AddChild();
					event.year = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКорпоративныхМероприятиях", "")[i].GetOptProperty("Год", "");
					event.type = oRes["ДополнительныеПоказатели"].GetOptProperty("УчастиеКорпоративныхМероприятиях", "")[i].GetOptProperty("Название", "");
				}
			}

			if(oRes["ДополнительныеПоказатели"].HasProperty("Публикации")){ //Публикации
				if(ArrayCount(teRes.avalable_publications)!=0){
					teRes.avalable_publications.Clear();
				}
				for(i=0;i<oRes["ДополнительныеПоказатели"].GetOptProperty("Публикации", "").length;i++){
					event = teRes.avalable_publications.AddChild();
					event.year = oRes["ДополнительныеПоказатели"].GetOptProperty("Публикации", "")[i].GetOptProperty("Год", "");
					event.count = oRes["ДополнительныеПоказатели"].GetOptProperty("Публикации", "")[i].GetOptProperty("КоличествоПубликаций", "");
					event.name = oRes["ДополнительныеПоказатели"].GetOptProperty("Публикации", "")[i].GetOptProperty("Название", "");
				}
			}


	}
	


}


// Создание/обновление справок.
function CreateResumes()
{
	try {
		
		if(!GLOBAL["CC_ZN_RESUMES"].is_update)
			return false

		var arrResumes = ZnRequestAPI("")

		GLOBAL["CC_ZN_RESUMES"].total = ArrayCount(arrResumes)


		var docRes = {}
		var teRes = {}
		var oRes = {}
		var isHasRes = false

		for(oRes in arrResumes)
		{
			colCode="";
			if (oRes["Сотрудник"].GetOptProperty("ПрефиксОрганизации", "")=="ЗБН"){

				colCode=StrReplace(oRes["Сотрудник"].GetOptProperty("ТабельныйНомер", ""), "ЗБН-", "") 
			}
			else{
				colCode=oRes["Сотрудник"].GetOptProperty("ТабельныйНомер", "")
			}
			isHasRes = HasWTObject("cc_zn_resumes", "col_code", colCode)
			if(isHasRes && !GLOBAL["CC_ZN_RESUMES"].is_update)
				continue;
			if(isHasRes){
				docRes = tools.obtain_doc_by_key( "cc_zn_resume", "col_code", colCode)

				teRes = docRes.TopElem

				fillResume(teRes)


				docRes.Save()
			}

			if(isHasRes)
				GLOBAL["CC_ZN_RESUMES"].updated++
		}
		

	} catch(e) {
		throw Error("CreateResumes -> " + e.message)
	}
}


function set_is_dismiss(){
	all_cards = XQuery("for $elem in cc_zn_resumes where ForeignElem( $elem/person_id )/is_dismiss=true() return $elem");

	for (c in all_cards){
		doc=tools.open_doc(c.id);
		doc.TopElem.col_is_dismiss=doc.TopElem.person_id.ForeignElem.is_dismiss;
		doc.Save();
	}
}



function Main()
{
	try {

		alert("----------------Начало. Агент Зарубеж Нефть #20178. Загрузка новых справок о сотрудниках через API------------------")
		var mainTimer = DateToRawSeconds(Date())

		CreateResumes();
		set_is_dismiss();//Проставление статуса уволен/не уволен в карточки справочника

		mainTimer = DateToRawSeconds(Date()) - mainTimer
		alert("Общая статистика:\n\n

		Справки о сотрудниках:
		Всего: " + GLOBAL["CC_ZN_RESUMES"].total + "
		Обновлено: " + GLOBAL["CC_ZN_RESUMES"].updated + "\n

		Агент завершил свою работу за " + mainTimer + " секунд
		")
		alert("----------------Конец. Агент Зарубеж Нефть #20178. Загрузка новых справок о сотрудниках через API------------------")

	} catch(e) {
		alert("Main -> " + e.message)
	}
}



Main()
