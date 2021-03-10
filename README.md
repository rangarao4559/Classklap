#classklap
<aura:component controller="SPOModuleController" implements="lightning:actionOverride,flexipage:availableForRecordHome,force:hasRecordId,force:lightningQuickActionWithoutHeader,force:appHostable,lightning:isUrlAddressable" access="global" >


    <aura:attribute name="adddata" type="String" />
   <aura:attribute name="accountMap" type="Map"/>
    <ltng:require scripts="{!$Resource.UtilityFiles}" afterScriptsLoaded="{!c.scriptsLoaded}"/>
    <aura:attribute name="spo" type="SPO__c"/>
    <aura:attribute name="LineItems" type="object"/>
    <aura:attribute name="totalVal" type="object"/>
    <aura:attribute name="newSPO"  type="boolean"  default="false"/>
    <aura:attribute name="selectedLookUpRecord" type="sObject" default="{}"/>
    <aura:attribute name="serviceLineItem" type="SPO_Line_Item__c[]"/>
    <aura:attribute name="recordId" type="Id"/>
    <aura:handler name="init" value="{!this}" action="{!c.doInit}"/>
    <aura:attribute name="statusPicklistValues" type="Object" default="Ops Rejected"/>
    <aura:attribute name="approveRejectVal" type="Object"/>
    <aura:attribute name="spoTypePicklistVal" type="Object"/>
    <aura:attribute name="classGroupPicklistVal" type="Object"/>
    <aura:attribute name="examinationPicklistVal" type="Object"/>
    <aura:attribute name="clientConfPicklistVal" type="Object"/>
    <aura:attribute name="advanceCollPicklistVal" type="Object"/>
    <aura:attribute name="advanceModePicklistVal" type="Object"/>
    <aura:attribute name="schemeCodePicklistVal" type="List"/>
    <aura:attribute name="cancelPicklistVal" type="Object"/>
    <aura:attribute name="serviceCodePicklistVal" type="Object"/>
    <aura:attribute name="schemeCodeSelectedValues" type="List" />
    <aura:attribute name="statePicklistVal" type="Object"/>
    <aura:attribute name="cityPicklistVal" type="Object"/>
    <aura:attribute name="examCodePicklistVal" type="Object"/>
    <aura:attribute name="gradePicklistVal" type="Object"/>
    
    <aura:attribute name="parentId" type="id"/>
    
    <aura:attribute name="saved" type="boolean" default="false"/>
    
    
    
    <!--<aura:attribute name="MetaOPSPicklistValues" type="Object"/>
    <aura:attribute name="deliveryPicklistValues" type="Object" />
    <aura:attribute name="billingPicklistValues" type="Object"/>-->
    <aura:attribute name="rejectionNotes" type="boolean" default="false"/>
    <aura:attribute name="spoTypePicklistValue" type="Object"/> 
    <aura:attribute name="deliveryPicklistValue" type="Object"/>
    
    <aura:attribute name="buttonClicked" type="string"/>  
    <aura:attribute name="fileNamehardCopyUpload" type="string"/>
    <aura:attribute name="fileNameApprovalUpload" type="string"/>
    <aura:attribute name="fileNamefinanceApproval" type="string"/>
    <aura:attribute name="fileNameexceptionApproval" type="string"/>
    
    <aura:attribute name="hardCopyUploadSpinner" type="boolean"/>
    <aura:attribute name="ApprovalUploadSpinner" type="boolean"/>
    <aura:attribute name="financeApprovalSpinner" type="boolean"/>
    <aura:attribute name="exceptionApprovalSpinner" type="boolean"/>
    <aura:attribute name="buttonstate" type="Boolean" default="false"/>
    
    <aura:attribute name="accState" type="String"/>
    <aura:attribute name="accCity" type="String"/>
    <aura:attribute name="listControllingValues" type="list" default="[]" description="to store controller field values"/>
    <aura:attribute name="listDependingValues" type="list" default="['--- None ---']" description="to store dependent field values"/>
    <aura:attribute name="depnedentFieldMap" type="map" description="map to store dependent values with controlling value"/>
    <aura:attribute name="controllingFieldAPI" type="string" default="Delivery_State__c" description="store field API name of Controller field"/>
    <aura:attribute name="dependingFieldAPI" type="string" default="Delivery_City__c" description="store field API name of dependent field"/>
    <aura:attribute name="controllingFieldAPI1" type="string" default="Billing_State__c" description="store field API name of Controller field"/>
    <aura:attribute name="dependingFieldAPI2" type="string" default="Billing_City__c" description="store field API name of dependent field"/>
    <aura:attribute name="bDisabledDependentFld" type="boolean" default="true"/> 
    <aura:attribute name="objDetail" type="SPO__c" default="{'sobjectType' : 'SPO__c'}"/>
     <aura:handler name="init" action="{!c.myAction}" value="{!this}" />
    
    <aura:attribute name="deliveryAddress" type="boolean" default="false"/>
    <aura:attribute name="billingAddress" type="boolean" default="false"/>
    <aura:attribute name="accountCode" type="boolean" default="false"/>
    <aura:attribute name="isModalOpen" type="boolean" default="false"/>
    <aura:attribute name="cancel" type="boolean" default="flase"/>
    <aura:attribute name="noOfOriginalOrder" type="integer"/>
    {!v.body}
    
    
    <aura:if isTrue="{!v.isModalOpen}">
        
        <section role="dialog" tabindex="-1" aria-labelledby="modal-heading-01" aria-modal="true" aria-describedby="modal-content-id-1" class="slds-modal slds-fade-in-open">
            <div class="slds-modal__container">
                <header class="slds-modal__header">
                    <lightning:buttonIcon iconName="utility:close"
                                          onclick="{! c.closeModel }"
                                          alternativeText="close"
                                          variant="bare-inverse"
                                          class="slds-modal__close"/>
                    <h2 id="modal-heading-01" class="slds-text-heading_medium slds-hyphenate">Update</h2>
                </header>
                <div class="slds-modal__content slds-p-around_medium" id="modal-content-id-1">
                    
                    <lightning:messages />
                    
                    <aura:if isTrue="{!v.deliveryAddress}">
                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                        <lightning:radioGroup name="radioGroup"
                                      label="Choose Existing Account Address"
                                      required="false"
                                      options="{!v.accountMap}"
                                      value="{!v.adddata}"    
                                      type="radio"/>
                                                      
                                    </lightning:layoutItem>
                        <lightning:input type="text" value="{!v.spo.Delivery_Street__c}" label="Delivery Street" required="false" aura:id="req"/>
                        <lightning:select name="select" value="{!v.spo.Delivery_City__c}" label="Delivery City" required="false"  disabled="{!v.bDisabledDependentFld}"  aura:id="req">
                            <aura:iteration items="{!v.listDependingValues}" var="city">
                                <option text="{!city}" value="{!city}" />
                            </aura:iteration>
                        </lightning:select>
                        <lightning:select name="select" value="{!v.spo.Delivery_State__c}" label="Delivery State" required="false"  onchange="{!c.onControllerFieldChange}" aura:id="req">
                            <aura:iteration items="{!v.listControllingValues}" var="state">
                                <option text="{!state}" value="{!state}"></option> 
                            </aura:iteration>
                        </lightning:select>
                        
                        <lightning:input type="text" value="{!v.spo.Delivery_Pin_Code__c}" label="Delivery Pincode" required="false"  aura:id="req"/>
                        <lightning:input type="text" value="{!v.spo.Delivery_Country__c}" label="Delivery Country" required="false"  aura:id="req"/>
                    </aura:if>
                    <aura:if isTrue="{!v.billingAddress}">
                        <lightning:input type="text" value="{!v.spo.Billing_Street__c}" label="Billing Street" required="true"  aura:id="req"/>
                        <lightning:select name="select" value="{!v.spo.Billing_City__c}" label="Billing City" required="true"   onchange="{!c.onControllerFieldChange}" aura:id="req">
                            <aura:iteration items="{!v.listDependingValues}" var="city">
                                <option text="{!city}" value="{!city}" />
                            </aura:iteration>
                        </lightning:select>
                        <lightning:select name="select" value="{!v.spo.Billing_State__c}" label="Billing State" required="true"  onchange="{!c.onControllerFieldChange}" aura:id="req">
                            <aura:iteration items="{!v.listControllingValues}" var="state">
                                <option text="{!state}" value="{!state}" />
                            </aura:iteration>
                        </lightning:select>
                        <lightning:input type="text" value="{!v.spo.Billing_Pin_Code__c}" label="Billing Pincode" required="true"  aura:id="req"/>
                        <lightning:input type="text" value="{!v.spo.Billing_Country__c}" label="Billing Country" required="true"  aura:id="req"/>
                        
                    </aura:if>
                    <aura:if isTrue="{!v.accountCode}">
                        <c:customLookup objectAPIName="account" IconName="standard:account" selectedRecord="{!v.selectedLookUpRecord}" label="Account Name"/>
                        <br/><br/>
                    </aura:if>        
                </div>
                <footer class="slds-modal__footer">
                    <lightning:button variant="neutral"
                                      label="Cancel"
                                      title="Cancel"
                                      onclick="{! c.closeModel }"/>
                    <lightning:button variant="brand"
                                      label="Save"
                                      title="Save"
                                      onclick="{!c.submitDetails}"/>
                </footer>
            </div>
        </section>
        <div class="slds-backdrop slds-backdrop_open"></div>
    </aura:if>    
    
    
    <div class="slds box">
        <div class="modal-header slds-modal__header slds-size_1-of-1">
            <h4 class="title slds-text-heading--medium" >SPO Form</h4>
        </div>
        <!--<div class="slds box">
        <div class="slds-utility-bar slds-align_absolute-center "  > 
            <lightning:button variant="brand" label="Save" onclick="{! c.saveSPO }" />
            <lightning:button variant="brand" label="Quick Save" onclick="{! c.quickSave }" />
            <lightning:button variant="brand" label="Cancel" onclick="{! c.cancel }" />
        </div>-->
        <!--<div class="slds-text-align_center">
            <div class="slds-global-header "> SPO FORM </div>
        </div>
        <div class="slds-grid slds-box slds-gutters styling ">-->
        <div class="slds-modal__content slds-p-around--x-small slds-align_absolute-center slds-size_1-of-1 slds-is-relative" aura:id="modalbody" id="modalbody">
            
            <form>
                <div class="slds-form-element custom-overLay">
                    
                    <lightning:accordion aura:id="accordion" activeSectionName="A">
                        <div class="slds-box">
                            <lightning:accordionSection  name="A" label="SPO Metadata">
                                <lightning:layout multipleRows="true">
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:input type="text" value="{!v.spo.SPO_Number__c}" label="SPO Number" required="true"  aura:id="req"/>
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input type="date" name="orderSignDate" label="Order Sign Date" value="{!v.spo.Order_Sign_Date__c}" required="true" aura:id="req" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input type="date" name="expectedDate" label="Expected Date of Delivery" required="true" aura:id="req" value="{!v.spo.Expected_Date_of_Delivery__c}" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <!--<lightning:outputField type="text" value="{!v.spo.Name}" label="SPO Name" />-->
                                        SPO Name : <ui:outputText value="{!v.spo.Name}" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        Deal Type : <ui:outputText value="{!v.spo.Deal_Source__c}" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:select name="select" value="{!v.spo.OPS_Verified_Reject_SPO_Metadata__c}" label="OPS Verified/Rejected" aura:id="OPSSPOMetaData"  onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="metaVerified">
                                                <option text="{!metaVerified}" value="{!metaVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    
                                    
                                    
                                    
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <!--<lightning:select name="select" value="{!v.spo.Status__c}" label="Status" >
                                            <aura:iteration items="{!v.statusPicklistValues}" var="status">
                                                <option text="{!status}"/>
                                            </aura:iteration>
                                        </lightning:select>
                                        <lightning:outputField type="text" value="{!v.spo.Status__c}" label="Status" />-->
                                        Status : <ui:outputText value="{!v.spo.Status__c}"  />
                                    </lightning:layoutItem>
                                     <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input type="date" name="OPS Approved Date" label="OPS Approved Date" value="{!v.spo.OPs_Approved_date__c}"  />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input type="date" name="Finance Approved Date" label="Finance Approved Date" value="{!v.spo.Finance_Approved_Date__c}"/>
                                    </lightning:layoutItem>
                                    
                                   <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                       Product Status : <ui:outputText value="{!v.spo.Product_Status__c}"  />
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input aura:id="HardCopy" onchange="{!c.handleFilesChange}" name ="hardCopyUpload"  label="Hard Copy Upload" type="file" required="true"    multiple="false"/>  
                                        <div class="slds-text-body_small slds-text-color_error">{!v.fileNamehardCopyUpload} </div>
                                        <aura:if isTrue="{!v.hardCopyUploadSpinner}">
                                            <div class="slds-text-body_small slds-text-color_error">Uploading... 
                                                <img src="/auraFW/resources/aura/images/spinner.gif" class="spinner-img" alt="Loading"/>'
                                            </div>
                                        </aura:if>
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input aura:id="Approval" name ="ApprovalUpload" onchange="{!c.handleFilesChange}"  label="Approvals Upload" type="file" required="false"  />  
                                        <div class="slds-text-body_small slds-text-color_error">{!v.fileNameApprovalUpload} </div>
                                        <aura:if isTrue="{!v.ApprovalUploadSpinner}">
                                            <div class="slds-text-body_small slds-text-color_error">Uploading... 
                                                <img src="/auraFW/resources/aura/images/spinner.gif" class="spinner-img" alt="Loading"/>'
                                            </div>
                                        </aura:if>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:select name="select" value="{!v.spo.SPO_Type__c}" label="SPO Type"  required="true" onchange="{!c.clientConfirmation}" aura:id="req">
                                            <aura:iteration items="{!v.spoTypePicklistValue}" var="SPOtype">
                                                <option text="{!SPOtype}" value="{!SPOtype}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.OPS_Metadata_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    
                                    
                                    
                                    
                                </lightning:layout>
                            </lightning:accordionSection>
                        </div> 
                        
                        <div class="slds-box">
                            <lightning:accordionSection  name="B" label="Account Data" >
                                <div class=" slds-box  slds-grid slds-grid--pull-padded ">
                                    <div class=" slds-col--padded">
                                        <div class="slds-box">
                                            <div class="slds-page-header">ORDER ACCOUNT DETAILS</div>
                                            <lightning:layout multipleRows="true" >
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    <lightning:button label="Update" onclick="{!c.onChangeClick}"  aura:id="deliveryAddress"/>
                                                </lightning:layoutItem>
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    <lightning:select name="select" value="{!v.spo.OPS_Verified_Reject_Delivery__c}" label="OPS Verified/Rejected"  aura:id="OPSDelivery"  onchange="{!c.disableSection}">
                                                        <aura:iteration items="{!v.approveRejectVal}" var="orderVerified">
                                                            <option text="{!orderVerified}" value="{!orderVerified}" />
                                                        </aura:iteration>
                                                    </lightning:select>
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small"> 
                                                    <lightning:textArea name="textArea" value="{!v.spo.OPS_Delivery_Rejection_Notes__c}" label="Rejection Notes" />
                                                </lightning:layoutItem>
                                                
                                                <!-- <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                        <lightning:radioGroup name="radioGroup"
                                      label="Industry"
                                      required="true"
                                      options="{!v.accountMap}"
                                      value="{!v.adddata}"
                                                              
                                      type="radio"/>
                                                      
                                    </lightning:layoutItem>-->
                                               
                                                      <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                        <lightning:select name="select" value="{!v.spo.Delivery_To__c}" label="Delivery To"    aura:id="req" onchange="">
                                            <aura:iteration items="{!v.deliveryPicklistValue}" var="SPOdeliveryTo">
                                                <option text="{!SPOdeliveryTo}" value="{!SPOdeliveryTo}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                                
                                            </lightning:layout>
                                            <div class="slds-page-header">Delivery Information</div>
                                            
                                            <lightning:layout multipleRows="true">
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Account Name : <ui:outputText value="{!v.spo.Delivery_Account_Name__c}" />
                                                    <!--<lightning:input type="text" name="deliveryStreet" label="Street" value="{!v.spo.Delivery_Street__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Account Code : <ui:outputText value="{!v.spo.Delivery_Account_Code__c}" />
                                                    <!--<lightning:input type="text" name="deliveryStreet" label="Street" value="{!v.spo.Delivery_Street__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Delivery Street : <ui:outputText value="{!v.spo.Delivery_Street__c}" />
                                                    <!--<lightning:input type="text" name="deliveryStreet" label="Street" value="{!v.spo.Delivery_Street__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Delivery City : <ui:outputText value="{!v.spo.Delivery_City__c}" />
                                                    <!--<lightning:input type="text" name="deliveryCity" label="City" value="{!v.spo.Delivery_City__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Delivery State : <ui:outputText value="{!v.spo.Delivery_State__c}" />
                                                    <!--<lightning:input type="text" name="deliveryState" label="State" value="{!v.spo.Delivery_State__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Delivery Country : <ui:outputText value="{!v.spo.Delivery_Country__c}" />
                                                    <!--<lightning:input type="text" name="deliveryState" label="State" value="{!v.spo.Delivery_State__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                    Delivery Pin Code : <ui:outputText value="{!v.spo.Delivery_Pin_Code__c}" />
                                                    <!--<lightning:input type="text" name="deliveryPincode" label="Pincode" value="{!v.spo.Delivery_Pin_Code__c}" disabled="{!v.OPSDelivery}"/>-->
                                                </lightning:layoutItem>
                                                
                                            </lightning:layout>
                                            <div class="slds-page-header">Service Contact Details (for delivery) Coordinator/Principal</div>
                                            <div class="">
                                                <lightning:layout multipleRows="true">
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        <!--Service Name : <ui:outputText value="{!v.spo.Service_Name__c}" />-->
                                                        <lightning:input type="text" value="{!v.spo.Service_Name__c}" label="Service Contact Person Name" required="true"  aura:id="req"/>
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        <lightning:input type="tel" name="servicePhone" required="true" label="Service Phone Number" value="{!v.spo.Service_Phone_Number__c}"   aura:id="req"/>
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        <lightning:input type="email" name="serviceEmail"   label="Service Email" value="{!v.spo.Service_Email_ID__c}"  required="true" aura:id="req"/>
                                                    </lightning:layoutItem>
                                                </lightning:layout>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="slds-box">
                                        <div class=" slds-col--padded">
                                            <div class="slds-page-header">INVOICE ACCOUNT DETAILS </div>
                                            
                                            <lightning:layout multipleRows="true">
                                                
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small" >
                                                    <lightning:buttonGroup>
                                                        <lightning:button label="Change" onclick="{!c.onChangeClick}" aura:id="accountCode"/>
                                                        <lightning:button label="Update" onclick="{!c.onChangeClick}"   aura:id="billingAddress"/>
                                                    </lightning:buttonGroup>
                                                </lightning:layoutItem>
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small" >
                                                    <lightning:select name="select" value="{!v.spo.OPS_Verified_Reject_Billing__c}" label="OPS Verified/Rejected" aura:id="OPSBilling"   onchange="{!c.disableSection}">
                                                        <aura:iteration items="{!v.approveRejectVal}" var="billingVerified">
                                                            <option text="{!billingVerified}" value="{!billingVerified}" />
                                                        </aura:iteration>
                                                    </lightning:select>
                                                </lightning:layoutItem>
                                                <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small"> 
                                                    <lightning:textArea name="textArea" value="{!v.spo.OPS_Billing_Rejection_Notes__c}" label="Rejection Notes" />
                                                </lightning:layoutItem>
                                            </lightning:layout>
                                            
                                            <div class="slds-page-header">Billing Information</div>
                                            <div class="">
                                                <lightning:layout multipleRows="true">
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Account Name : <ui:outputText value="{!v.spo.Billing_Account_Name__c}" />
                                                    </lightning:layoutItem>
                                                    
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Account Code : <ui:outputText value="{!v.spo.Billing_Account_Code__c}" />
                                                    </lightning:layoutItem>
                                                    
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Billing Street : <ui:outputText value="{!v.spo.Billing_Street__c}" />
                                                        <!--lightning:input type="text" name="billingStreet" label="Street" value="{!v.spo.Billing_Street__c}" disabled="{!v.OPSBilling}"/>-->
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Billing City : <ui:outputText value="{!v.spo.Billing_City__c}" />
                                                        <!--<lightning:input type="text" name="billingCity" label="City" value="{!v.spo.Billing_City__c}" disabled="{!v.OPSBilling}"/>-->
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Billing State : <ui:outputText value="{!v.spo.Billing_State__c}" />
                                                        <!--<lightning:input type="text" name="billingState" label="State" value="{!v.spo.Billing_State__c}" disabled="{!v.OPSBilling}"/>-->
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Billing Country : <ui:outputText value="{!v.spo.Billing_Country__c}" />
                                                        <!--<lightning:input type="text" name="billingState" label="State" value="{!v.spo.Billing_State__c}" disabled="{!v.OPSBilling}"/>-->
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Billing Pincode : <ui:outputText value="{!v.spo.Billing_Pin_Code__c}" />
                                                        <!--<lightning:input type="text" name="billingPincode" label="Pincode" value="{!v.spo.Billing_Pin_Code__c}" disabled="{!v.OPSBilling}"/>-->
                                                    </lightning:layoutItem>
                                                </lightning:layout>
                                            </div>
                                            
                                            <div class="slds-page-header">Signatory Contact Details (for billing)</div>
                                            <div class="">
                                                <lightning:layout multipleRows="true">
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        Signatory Name : <ui:outputText value="{!v.spo.Signatory_Name__c}" />
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        <lightning:input type="tel" name="signatoryPhone"  label="Signatory Phone Number" value="{!v.spo.Signatory_Phone_Number__c}"  required="true" aura:id="req"/>
                                                    </lightning:layoutItem>
                                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                                        <lightning:input type="email" name="signatoryEmail" label="Signatory Email" value="{!v.spo.Signatory_Email_ID__c}"  required="true" aura:id="req"/>
                                                    </lightning:layoutItem>
                                                </lightning:layout>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </lightning:accordionSection>
                        </div>
                        <div class="slds-box">
                            <lightning:accordionSection label="Discount Data and Order Data">
                                <div class="slds-box">
                                    <div class="slds-page-header">DISCOUNT DATA</div>
                                    <lightning:layout multipleRows="true">
                                        <lightning:layoutItem size="12" smallDeviceSize="6" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small" >
                                            <lightning:select name="discountOPS" value="{!v.spo.OPS_Verified_Reject_Discount__c}" label="OPS Verified/Reject"  aura:id="OPSDiscountData"  onchange="{!c.disableSection}">
                                                <aura:iteration items="{!v.approveRejectVal}" var="deliveryVerified">
                                                    <option text="{!deliveryVerified}" value="{!deliveryVerified}" />
                                                </aura:iteration>
                                            </lightning:select>
                                        </lightning:layoutItem>
                                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small"> 
                                            <lightning:textArea name="textArea" value="{!v.spo.OPS_Discount_Rejection_Notes__c}" label="Rejection Notes"/>
                                        </lightning:layoutItem>
                                        
                                        <lightning:layoutItem size="12" smallDeviceSize="6" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                            <!--School Discount : <ui:outputText value="{!v.spo.School_Discount__c}" />%-->
                                            <lightning:input type="number" name="percentValue" value="{!v.spo.School_Discount__c}" onchange="{!c.updateLineItems}" label="School Discount" required="true" />
                                        </lightning:layoutItem  >
                                        
                                        <lightning:layoutItem size="12" smallDeviceSize="6" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                            CP Discount : <ui:outputText value="{!v.spo.CP_Discount__c}" />%
                                            <!--<lightning:input type="number" name="cpPercent" value="{!v.spo.CP_Discount__c}" label="CP Discount" disabled="{!v.OPSDiscountData}"/>-->
                                        </lightning:layoutItem>
                                    </lightning:layout>
                                    <div class="slds-box">
                                        <div class="slds-page-header">ORDER DATA <lightning:button variant="brand" label="Add" onclick="{! c.addProductLI }" disabled="{!v.spo.Status__c == 'Finance Approved'}" /></div>
                                        <div class="slds" style="overflow-x:auto;">
                                            <div style="width:100%">
                                                <table class="slds-table slds-table--bordered slds-table--cell-buffer slds-max-large-table--stacked-horizontal" style="width:100%" >
                                                    <thead>
                                                        <tr class="slds-text-heading--label">
                                                            <th scope="col" class="nobordertop" title="Grade" >
                                                                <div>Grade</div>
                                                            </th>
                                                            <th scope="col" class="nobordertop" title="Product" >
                                                                <div>Product</div>
                                                            </th>
                                                            <th scope="col" class="nobordertop" title="Quantity" >
                                                                <div>Quantity</div>
                                                            </th>
                                                            <th scope="col" class="nobordertop" title="Discount%">
                                                                <div>Discount%</div>
                                                            </th>
                                                             <th scope="col" class="nobordertop" title="Discount%">
                                                                <div>Overall Discount%</div>
                                                            </th>
                                                            <th scope="col" class="nobordertop" title="MRP">
                                                                <div>MRP</div>
                                                            </th>
                                                            <th scope="col" class="nobordertop" title="Final Price">
                                                                <div>Final Price</div>
                                                            </th>
                                                            <th scope="col" class="nobordertop" title="Cancellation">
                                                                <div>Cancellation</div>
                                                            </th>
                                                        </tr>
                                                    </thead>
                                                    <tbody>
                                                        
                                                        <aura:iteration items="{!v.LineItems.productlineitems}" var="spopli" indexVar="index">
                                                            <tr>
                                                                <td data-label="Grade"  >
                                                                    <!--<div><ui:outputText value="{!spopli.productLineItem.Grade__c}" /></div>-->
                                                                    <div>
                                                                        <aura:if isTrue="{!spopli.productLineItem.Product__c == null}">
                                                                            <lightning:select label="Grade" value="{!spopli.productLineItem.Grade__c}"  required="true" aura:id="req" >
                                                                                <aura:iteration items="{!v.gradePicklistVal}" var="grade">
                                                                                    <option text="{!grade}" value="{!grade}" />
                                                                                </aura:iteration>
                                                                            </lightning:select>
                                                                            <aura:set attribute="else">
                                                                                <ui:outputText value="{!spopli.productLineItem.Grade__c}"/>
                                                                            </aura:set>
                                                                        </aura:if>
                                                                    </div>
                                                                </td>
                                                                <td data-label="Product">
                                                                    <div>
                                                                        <aura:if isTrue="{!spopli.productLineItem.Product__c == null}">
                                                                            <c:customLookupforProduct objectAPIName="Product2" IconName="standard:Product" selectedRecord="{!spopli.product}" label="Product Name" extraFilter="{!spopli.productLineItem.Grade__c}"/>  
                                                                            <aura:set attribute="else">
                                                                                <ui:outputText value="{!spopli.product.Name}"/>
                                                                            </aura:set>
                                                                        </aura:if>
                                                                    </div>
                                                                </td>
                                                                <td data-label="Quantity">
                                                                    <div><lightning:input value="{!spopli.productLineItem.Quantity__c}" label="Quantity" type="number" onchange="{!c.updateLineItems}" name="{!index}"  required="true" min="1" aura:id="req"/></div>
                                                                </td>
                                                                <td data-label="Discount">
                                                                    <div><lightning:input value="{!spopli.productLineItem.Discount__c}" label="Discount" type="number" onchange="{!c.updateLineItems}" name="{!index}"    max="100"  aura:id="req"/></div>
                                                                </td>
                                                                <td data-label="Overall Discount">
                                                                    <div><ui:outputText value="{!spopli.productLineItem.Overall_Percentage__c}"/></div>
                                                                </td>
                                                                <td data-label="MRP">
                                                                    <div><ui:outputCurrency value="{!spopli.productLineItem.MRP__c}" /></div>
                                                                </td>
                                                                <td data-label="Final Price">
                                                                    <div><ui:outputCurrency value="{!spopli.productLineItem.Final_Price__c}"/></div>
                                                                </td>
                                                                <td data-label="Cancellation">
                                                                    <div>
                                                                        <lightning:select label="Cancellation" value="{!spopli.productLineItem.Cancellation__c}"     onchange="{!c.updateStatus}">
                                                                            <aura:iteration items="{!v.cancelPicklistVal}" var="cancel">
                                                                                <option text="{!cancel}" value="{!cancel}" />
                                                                            </aura:iteration>
                                                                        </lightning:select>
                                                                    </div>
                                                                </td>
                                                                
                                                                <td data-label="Remove">
                                                                    <a  onclick="{!c.removeRow}" id="{!index}">
                                                                        <lightning:buttonIcon iconName="utility:delete" alternativeText="delete" />
                                                                    </a>
                                                                </td>

                                                            </tr>
                                                            <br/><br/><br/>
                                                        </aura:iteration>
                                                    </tbody>
                                                </table> 
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </lightning:accordionSection>
                            
                        </div>
                        <div class="slds-box">
                            <lightning:accordionSection label="Other Offering Services">
                                
                                <lightning:layout multipleRows="true">
                                    <lightning:layoutItem size="12" smallDeviceSize="6" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small" >
                                        <lightning:select name="otherOPS" value="{!v.spo.OPS_Verified_Reject_Other_Service__c}"  label="OPS Verified/Reject" aura:id="OPSOtherService"  onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="financeVerified">
                                                <option text="{!financeVerified}" value="{!financeVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.OPS_Other_Offering_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="6" padding="around-small">
                                        
                                    </lightning:layoutItem>
                                    
                                    <table class="slds-table slds-table--bordered slds-table--cell-buffer slds-max-medium-table--stacked-horizontal">
                                        <thead>
                                            <tr class="slds-text-heading--label">
                                                <th scope="col" class="nobordertop" title="Class Group">
                                                    <div>Class Group</div>
                                                </th>
                                                <th scope="col" class="nobordertop" title="Exam Code" required="true">
                                                    <div>Exam Code</div>
                                                </th>
                                                <th scope="col" class="nobordertop" title="Service Code">
                                                    <div>Service Code</div>
                                                </th>
                                                <th scope="col" class="nobordertop" title="Examination Term" required="true">
                                                    <div>Examination Term</div>
                                                </th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <aura:iteration items="{!v.serviceLineItem}" var="sposli">
                                                <tr>
                                                    <td data-label="Class Group">
                                                        <div>
                                                            <ui:outputText value="{!sposli.Class_Group__c}" />
                                                            <!--<lightning:select name="select" value="{!sposli.Class_Group__c}" disabled="{!v.OPSOtherService}" required="true">
                                                                <aura:iteration items="{!v.classGroupPicklistVal}" var="classGroup">
                                                                    <option text="{!classGroup}"/>
                                                                </aura:iteration>
                                                            </lightning:select>-->
                                                        </div>
                                                    </td>
                                                    <td data-label="Exam Code">
                                                        <div>
                                                            <lightning:select label="Exam Code" name="select" value="{!sposli.Exam_Code__c}"  required="true" aura:id="req">
                                                                <aura:iteration items="{!v.examCodePicklistVal}" var="ExamCode">
                                                                    <option text="{!ExamCode}" value="{!ExamCode}" />
                                                                </aura:iteration>
                                                            </lightning:select>
                                                        </div>
                                                    </td>
                                                    <td data-label="Service Code">
                                                        <div>
                                                            <lightning:select label="Service Code" name="select" value="{!sposli.Service_Code__c}"   required="true" aura:id="req">
                                                                <aura:iteration items="{!v.serviceCodePicklistVal}" var="serviceCode">
                                                                    <option text="{!serviceCode}" value="{!serviceCode}" />
                                                                </aura:iteration>
                                                            </lightning:select>
                                                        </div>
                                                    </td>
                                                    <td data-label="Examination Term">
                                                        <div>
                                                            <lightning:select label="Examination Term" name="select" value="{!sposli.Examination_Term__c}"   required="true" aura:id="req">
                                                                <aura:iteration items="{!v.examinationPicklistVal}" var="examinationTerm">
                                                                    <option text="{!examinationTerm}" value="{!examinationTerm}" />
                                                                </aura:iteration>
                                                            </lightning:select>
                                                        </div>
                                                    </td>
                                                </tr>
                                            </aura:iteration>
                                        </tbody>
                                    </table>
                                </lightning:layout>
                            </lightning:accordionSection>
                        </div>
                        <div class="slds-box">
                            <lightning:accordionSection label="Finance Details">
                                <lightning:layout multipleRows="true">
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:select name="select" value="{!v.spo.OPS_Verified_Reject_Finance__c}" label="OPS Verified/Rejected" aura:id="OPSFinanceDetails"  onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="financeVerified">
                                                <option text="{!financeVerified}" value="{!financeVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.OPS_Finance_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:select name="select" value="{!v.spo.FIN_Verified_Reject_Finance__c}" label="FIN Verified/Rejected" aura:id="FINFinanceDetails"  onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="FINVerified">
                                                <option text="{!FINVerified}" value="{!FINVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.FIN_Finance_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        Total DV Before Discount : <ui:outputCurrency value="{!v.totalVal.totalBefore}" />
                                        <!--<lightning:input type="text" label="Total DV Before Discount" disabled="{!v.OPSFinanceDetails}"/>-->
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        Total Discount Value : <ui:outputCurrency value="{!v.totalVal.totalDiscount}" />
                                        <!--<lightning:input type="text" label="Total Discount Value" disabled="{!v.OPSFinanceDetails}"/>-->
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        Total DV after Discount : <ui:outputCurrency value="{!v.totalVal.totalAfter}" />
                                        <!--<lightning:input type="text" label="Total DV after Discount" disabled="{!v.OPSFinanceDetails}"/>-->
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <!--<lightning:select name="select" value="{!v.spo.Client_Confirmation_Status__c}" label="Client Confirmation Status" disabled="{!v.spo.OPS_Verified_Reject_Finance__c == 'Verified'}">
                                            <aura:iteration items="{!v.clientConfPicklistVal}" var="clientConfirmation">
                                                <option text="{!clientConfirmation}" value="{!clientConfirmation}" />
                                            </aura:iteration>
                                        </lightning:select>-->
                                        Client Confirmation Status : <ui:outputText value="{!v.spo.Client_Confirmation_Status__c}" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:buttonStateful
                                                                  labelWhenOff="Send OTP"
                                                                  labelWhenOn="OTP Sent"
                                                                  iconNameWhenOff="utility:answer"
                                                                  iconNameWhenOn="utility:check"
                                                                  labelWhenHover="Resend"
                                                                  iconNameWhenHover="utility:answer"
                                                                  state="{! v.buttonstate }"
                                                                  onclick="{! c.sendOTP }"
                                                                  />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:input value="{!v.spo.OTP_Entered__c}" label="OTP" required="{!v.spo.SPO_Type__c == 'Additional'}" pattern="[0-9]{4,5}"  onchange="{!c.clientConfirmation}" max="10000" aura:id="OTP"/>
                                    </lightning:layoutItem>
                                </lightning:layout>
                                <div class="slds-page-header">Advance Details</div>
                                <div class="">   
                                    <lightning:layout multipleRows="true">
                                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                            <lightning:input value="{!v.spo.Advance_Amount__c}" label="Advance Amount" required="true" aura:id="req" type="currency" formatter="currency" currencyCode="INR"  class="{! v.spo.Advance_Amount__c lt v.totalVal.totalAfter*0.10 ? ' tier-one ' :''}"/>
                                        </lightning:layoutItem>
                                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                            <lightning:select name="select" value="{!v.spo.Advance_Collected_From__c}" label="Advance Collected From" aura:id="req" required="true" >
                                                <aura:iteration items="{!v.advanceCollPicklistVal}" var="advanceCollected">
                                                    <option text="{!advanceCollected}" value="{!advanceCollected}" />
                                                </aura:iteration>
                                            </lightning:select>
                                        </lightning:layoutItem>
                                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                            <lightning:input type="text" value="{!v.spo.Advance_Reference_Number__c}" label="Advance Reference Number" required="true" aura:id="req" />
                                        </lightning:layoutItem>
                                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                            <lightning:select name="select" value="{!v.spo.Advance_Mode__c}" label="Advance Mode" required="true" aura:id="req" >
                                                <aura:iteration items="{!v.advanceModePicklistVal}" var="advanceMode">
                                                    <option text="{!advanceMode}" value="{!advanceMode}" />
                                                </aura:iteration>
                                            </lightning:select>
                                        </lightning:layoutItem>
                                        <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                            <lightning:input type="date" name="advanceDate" label="Advance Date" value="{!v.spo.Advance_Date__c}"  aura:id="req" required="true"/>
                                        </lightning:layoutItem>
                                    </lightning:layout>
                                </div>
                            </lightning:accordionSection>
                        </div>
                        
                        
                        <div class="slds-box">
                            <lightning:accordionSection label="Other Finance Offering">
                                <lightning:layout multipleRows="true">
                                    <lightning:layoutItem size="12" smallDeviceSize="6" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:select name="otherOPS" value="{!v.spo.OPS_Verified_Reject_Other_Finance__c}" label="OPS Verified/Reject"  aura:id="OPSOtherFinance"  onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="otherFinanceVerified">
                                                <option text="{!otherFinanceVerified}" value="{!otherFinanceVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.OPS_Other_Finance_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:select name="select" value="{!v.spo.FIN_Verified_Reject_Other_Finance__c}" label="FIN Verified/Rejected"  aura:id="FINOtherFinance"  onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="FINVerified">
                                                <option text="{!FINVerified}" value="{!FINVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.OPS_Finance_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <!--<lightning:select name="select" value="{!v.spo.Scheme_Code__c}" label="Scheme Code" required="true" aura:id="req" disabled="{!v.spo.OPS_Verified_Reject_Other_Finance__c == 'Verified'}" >
                                            <aura:iteration items="{!v.schemeCodePicklistVal}" var="schemeCode">
                                                <option text="{!schemeCode}" value="{!schemeCode}" />
                                            </aura:iteration>
                                        </lightning:select>-->
                                        <lightning:dualListbox aura:id="req"
                                                               name="Scheme Code"
                                                               label="Scheme Code"
                                                               sourceLabel="Available Scheme Code"
                                                               selectedLabel="Selected Scheme Code"
                                                               options="{!v.schemeCodePicklistVal }"
                                                               value="{!v.schemeCodeSelectedValues}"
                                                              
                                                               required="true"
                                                               />
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input aura:id="financeApproval" name ="financeApproval" onchange="{!c.handleFilesChange}"  label="Finance Approvals Upload" type="file" required="false" />  
                                        <div class="slds-text-body_small slds-text-color_error">{!v.fileNamefinanceApproval} </div>
                                        <aura:if isTrue="{!v.financeApprovalSpinner}">
                                            <div class="slds-text-body_small slds-text-color_error">Uploading... 
                                                <img src="/auraFW/resources/aura/images/spinner.gif" class="spinner-img" alt="Loading"/>'
                                            </div>
                                        </aura:if>
                                    </lightning:layoutItem>
                                    
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small">
                                        <lightning:input aura:id="exceptionApproval" name ="exceptionApproval" onchange="{!c.handleFilesChange}"  label="Exception Approvals Upload" type="file" required="false" /> 
                                        <div class="slds-text-body_small slds-text-color_error">{!v.fileNameexceptionApproval} </div>
                                        <aura:if isTrue="{!v.financeApprovalSpinner}">
                                            <div class="slds-text-body_small slds-text-color_error">Uploading... 
                                                <img src="/auraFW/resources/aura/images/spinner.gif" class="spinner-img" alt="Loading"/>'
                                            </div>
                                        </aura:if>
                                    </lightning:layoutItem>
                                </lightning:layout>
                            </lightning:accordionSection>
                            
                        </div>
                        <div class="slds-box">
                            <lightning:accordionSection label="Payment Terms">
                                
                                <lightning:layout multipleRows="true">
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:select name="paymentFin" value="{!v.spo.FIN_Verified_Reject_Payment__c}" label="FIN Verified/Reject"  aura:id="FINPayment" onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="paymentVerified">
                                                <option text="{!paymentVerified}" value="{!paymentVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small"> 
                                        <lightning:textArea name="textArea" value="{!v.spo.FIN_Payment_Rejection_Notes__c}" label="Rejection Notes" />
                                    </lightning:layoutItem>
                                    <lightning:layoutItem size="12" smallDeviceSize="12" mediumDeviceSize="6" largeDeviceSize="4" padding="around-small" >
                                        <lightning:select name="paymentFinAttach" value="{!v.spo.FIN_Verified_Reject_Attachment__c}" label="FIN Verified/Reject Attachment"  aura:id="FINPayment" onchange="{!c.disableSection}">
                                            <aura:iteration items="{!v.approveRejectVal}" var="attachVerified">
                                                <option text="{!attachVerified}" value="{!attachVerified}" />
                                            </aura:iteration>
                                        </lightning:select>
                                    </lightning:layoutItem>
                                </lightning:layout>
                                <div style="height: 300px">
                                    <table class="slds-table slds-table--bordered slds-table--cell-buffer slds-max-medium-table--stacked-horizontal">
                                        <thead>
                                            <tr class="slds-text-heading--label">
                                                <th scope="col" class="nobordertop" title="School Payment Terms">
                                                    <div>School Payment Terms</div>
                                                </th>
                                                <th scope="col" class="nobordertop" title="Standard Term">
                                                    <div>Standard Term</div>
                                                </th>
                                                <th scope="col" class="nobordertop" title="% Revised Payment Term">
                                                    <div>% Revised Payment Term</div>
                                                </th>
                                                <th scope="col" class="nobordertop" title="Notes">
                                                    <div>Notes</div>
                                                </th>
                                            </tr>
                                        </thead>
                                        <tbody>
                                            <aura:iteration items="{!v.LineItems.paymentlineitems}" var="spopli">
                                                <tr>
                                                    <td data-label="School Payment Term">
                                                        <div>
                                                            <ui:outputText value="{!spopli.paymentLineItem.School_Payment_Terms__c}"/>
                                                        </div>
                                                    </td>
                                                    <td data-label="Standard Term">
                                                        <div>
                                                            <ui:outputText value="{!spopli.paymentLineItem.Standard_Term__c}"/>
                                                        </div>
                                                    </td>
                                                    <td data-label="% Revised Payment Term">
                                                        <div>
                                                            <lightning:input type="text" name="revised" value="{!spopli.paymentLineItem.Revised_Payment_Term__c}" />
                                                        </div>
                                                    </td>
                                                    <td data-label="Notes">
                                                        <div>
                                                            <ui:inputTextArea value="{!spopli.paymentLineItem.Notes__c}"  rows="4"/>
                                                        </div>
                                                    </td>
                                                </tr>
                                            </aura:iteration>
                                        </tbody>
                                    </table>
                                    <br/><br/><br/>
                                </div>
                            </lightning:accordionSection>
                        </div>
                        
                    </lightning:accordion>
                </div>
            </form>
        </div>
        <div class="modal-footer slds-modal__footer slds-size_1-of-1">
            <div class="slds-utility-bar slds-align_absolute-center "  > 
                <lightning:button variant="brand" label="Save" onclick="{! c.saveSPO }" aura:id="Save"/>
                <!--<lightning:button variant="brand" label="Quick Save" onclick="{! c.saveSPO }" aura:id="QuickSave"/>-->
                <lightning:button variant="brand" label="Cancel" onclick="{! c.cancel }" />
            </div>
        </div>
    </div>
    <aura:html tag="style">
        .cuf-content {
        padding: 0 0rem !important;
        }
        .slds-p-around--medium {
        padding: 0rem !important;
        }       
        .slds-modal__content{
        overflow-y:hidden !important;
        height:unset !important
        max-height:unset !important;
        }
    </aura:html>
    
</aura:component>



=======================================
Controller===
===================
({
    myAction : function(component, event, helper) {
       // helper.helperMethod(component);
        var controllingFieldAPI = component.get("v.controllingFieldAPI");
        var dependingFieldAPI = component.get("v.dependingFieldAPI");
        var controllingFieldAPI1 = component.get("v.controllingFieldAPI1");
        var dependingFieldAPI2 = component.get("v.dependingFieldAPI2"); 
        var objDetails = component.get("v.objDetail");
        //alert(controllingFieldAPI+'---'+dependingFieldAPI+'---'+objDetails);
        // call the helper function
        helper.fetchPicklistValues(component,objDetails,controllingFieldAPI, dependingFieldAPI);
        helper.fetchPicklistValues(component,objDetails,controllingFieldAPI1, dependingFieldAPI2);
        //helper.getaccount(component, event, helper);
    },
    scriptsLoaded : function(component, event, helper) {
        console.log('Script loaded');
        if ($A.get("$Browser.formFactor") === 'PHONE') {
            $A.createComponent(
                "aura:html",
                {
                    "aura:id": "styleTag",
                    "tag": "style",
                    "body": '.custom-overLay {overflow : scroll; height:' + ($(window).height() - 100) + 'px;}'
                },
                function(newHtml, status, errorMessage){
                    //Add the new button to the body array
                    if (status === "SUCCESS") {
                        var body = component.get("v.body");
                        body.push(newHtml);
                        component.set("v.body", body);
                    }
                    else if (status === "INCOMPLETE") {
                        console.log("No response from server or client is offline.")
                        // Show offline error
                    }
                        else if (status === "ERROR") {
                            console.log("Error: " + errorMessage);
                            // Show error message
                        }
                }
            );
        }
    },
    doInit : function(component, event, helper) {
        
        helper.getStatus(component, event, helper);
        helper.getPicklistValue(component, event, helper, 'SPO__c','Status__c','statusPicklistValues');
        helper.getPicklistValue(component, event, helper, 'SPO__c','OPS_Verified_Reject_SPO_Metadata__c','approveRejectVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','SPO_Type__c','spoTypePicklistValue');
        helper.getPicklistValue(component, event, helper, 'SPO_Line_Item__c','Class_Group__c','classGroupPicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO_Line_Item__c','Examination_Term__c','examinationPicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','Scheme_Code__c','schemeCodePicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO_Line_Item__c','Cancellation__c','cancelPicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','Client_Confirmation_Status__c','clientConfPicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','Advance_Mode__c','advanceModePicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','Advance_Collected_From__c','advanceCollPicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO_Line_Item__c','Exam_Code__c','examCodePicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','Billing_State__c','statePicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO__c','Billing_City__c','cityPicklistVal');
        helper.getPicklistValue(component,event, helper, 'SPO__c','Delivery_To__c','deliveryPicklistValue');
        helper.getPicklistValue(component, event, helper, 'SPO_Line_Item__c','Service_Code__c','serviceCodePicklistVal');
        helper.getPicklistValue(component, event, helper, 'SPO_Line_Item__c','Grade__c','gradePicklistVal');
        //debugger;
        console.log(component.get("v.serviceCodePicklistVal"));
        //helper.addProduct(component, event, helper);
    },
    
    disableSection : function(component, event, helper) {
        debugger;
        var spo = component.get("v.spo");
        var overAllOPS;
        var overAllFIN; 
        var oneOPS;
        var oneFIN;
        if(spo.OPS_Verified_Reject_Billing__c == 'Verified' && spo.OPS_Verified_Reject_Delivery__c == 'Verified' &&
           spo.OPS_Verified_Reject_Discount__c == 'Verified' && spo.OPS_Verified_Reject_Finance__c == 'Verified' &&
           spo.OPS_Verified_Reject_Other_Finance__c == 'Verified' && spo.OPS_Verified_Reject_Other_Service__c == 'Verified' &&
           spo.OPS_Verified_Reject_SPO_Metadata__c == 'Verified' ){
            overAllOPS = true;
        }
        if(spo.OPS_Verified_Reject_Billing__c == 'Verified' || spo.OPS_Verified_Reject_Delivery__c == 'Verified' ||
           spo.OPS_Verified_Reject_Discount__c == 'Verified' || spo.OPS_Verified_Reject_Finance__c == 'Verified' ||
           spo.OPS_Verified_Reject_Other_Finance__c == 'Verified' || spo.OPS_Verified_Reject_Other_Service__c == 'Verified' ||
           spo.OPS_Verified_Reject_SPO_Metadata__c == 'Verified' ){
            oneOPS = true;
        }
        if(spo.OPS_Verified_Reject_Billing__c == 'Rejected' || spo.OPS_Verified_Reject_Delivery__c == 'Rejected' ||
           spo.OPS_Verified_Reject_Discount__c == 'Rejected' || spo.OPS_Verified_Reject_Finance__c == 'Rejected' ||
           spo.OPS_Verified_Reject_Other_Finance__c == 'Rejected' || spo.OPS_Verified_Reject_Other_Service__c == 'Rejected' ||
           spo.OPS_Verified_Reject_SPO_Metadata__c == 'Rejected' ){
            overAllOPS = false;
        }
        if(spo.FIN_Verified_Reject_Attachment__c == 'Verified' ||spo.FIN_Verified_Reject_Finance__c == 'Verified' ||
           spo.FIN_Verified_Reject_Other_Finance__c == 'Verified' || spo.FIN_Verified_Reject_Payment__c == 'Verified' ){
            oneFIN = true;
        }
        
        if(spo.FIN_Verified_Reject_Attachment__c == 'Verified' && spo.FIN_Verified_Reject_Finance__c == 'Verified' &&
           spo.FIN_Verified_Reject_Other_Finance__c == 'Verified' && spo.FIN_Verified_Reject_Payment__c == 'Verified' ){
            overAllFIN = true;
        }
        else if(spo.FIN_Verified_Reject_Attachment__c == 'Rejected' || spo.FIN_Verified_Reject_Finance__c == 'Rejected' ||
                spo.FIN_Verified_Reject_Other_Finance__c == 'Rejected' || spo.FIN_Verified_Reject_Payment__c == 'Rejected' ){
            overAllFIN = false;
        }
        if(overAllOPS == true){
            spo.Status__c = 'Ops Approved';
             var today = $A.localizationService.formatDate(new Date(), "YYYY-MM-DD");
            spo.OPs_Approved_date__c=today;
        }
        if((overAllOPS == true && overAllFIN == true) ){//|| (overAllFIN == true || oneFIN == true)
            spo.Status__c = 'Finance Approved';
             var today = $A.localizationService.formatDate(new Date(), "YYYY-MM-DD");
            spo.Finance_Approved_Date__c=today;
        }
        if(overAllFIN == false ){
            spo.Status__c = 'Finance Rejected';
        }
        if(overAllOPS == false){
            spo.Status__c = 'Ops Rejected';
        }
        component.set("v.spo",spo);
    },	
    
    
    
    handleFilesChange: function(component, event, helper) {
        var fileName = 'No File Selected..';
        var name = event.getSource().get("v.name");
        //alert(name);
        if (event.getSource().get("v.files").length > 0) {
            fileName = event.getSource().get("v.files")[0]['name'];
        }
        component.set("v.fileName"+name, fileName);
    },
    
    clientConfirmation : function(component, event, helper) {
        var check = event.getSource().getLocalId();
        var spo = component.get("v.spo");
        if(check == 'req'){
            if(spo.SPO_Type__c == 'Additional'){
                spo.Client_Confirmation_Status__c = 'Required';
            }
            else{
                spo.Client_Confirmation_Status__c = 'Not Required';
            }
        }
        else{
            if(spo.OTP_Sent__c == spo.OTP_Entered__c ){
                spo.Client_Confirmation_Status__c = 'Confirmed';
            }
            else{
                spo.Client_Confirmation_Status__c = 'Not Confirmed';
            }
        }
        component.set("v.spo",spo);
    },
    
    sendOTP : function(component, event, helper) {
        var buttonstate = component.get("v.buttonstate");
        component.set('v.buttonstate', !buttonstate);
        var spo = component.get("v.spo");
        if(spo.SPO_Type__c == 'Additional'){
            var action = component.get("c.sendOTPSMS");
            action.setParams({
                "spoRecord": spo
            });
            action.setCallback(this, function(response) {
                component.set("v.spo",response.getReturnValue());
            });
            $A.enqueueAction(action);
        }
        else{
            alert('SPO Type is not Additional');
        }
    },
    
    updateLineItems : function(component, event, helper) {
        var lineItems = component.get("v.LineItems");
        debugger;
        var spo = component.get("v.spo");
        debugger;
        //var grades = new List(); 
        var grades =  [];
        var totalBefore = 0;
        var totalAfter = 0;
        var totalDiscount = 0;
        var ldiscount;
        if(spo.Deal_Source__c == null || spo.Deal_Source__c == undefined)
            ldiscount = 0;
        else if(spo.Deal_Source__c == 'CP')
            ldiscount = spo.CP_Discount__c;
            else
                ldiscount = spo.School_Discount__c;
        
       for(var temp in lineItems.productlineitems){
            lineItems.productlineitems[temp].productLineItem.Product__c = lineItems.productlineitems[temp].product.Id ;    
            if(lineItems.productlineitems[temp].productLineItem.Product__c){
                var pdiscount = lineItems.productlineitems[temp].product.Discount__c;
                var finaldiscount = parseFloat(ldiscount) + parseFloat(pdiscount);
                
                /*if(event.getSource().get("v.label") == 'Quantity'){
                    if(!lineItems.productlineitems[temp].productLineItem.Discount__c)
                    lineItems.productlineitems[temp].productLineItem.Discount__c = finaldiscount;
                    
                }
                
                else{/*/
                lineItems.productlineitems[temp].productLineItem.Overall_Percentage__c = parseFloat(finaldiscount) + parseFloat(lineItems.productlineitems[temp].productLineItem.Discount__c);
                if(event){
                    if(event.getSource().get("v.label") == 'Discount')
                    if(event.getParam("oldValue") != event.getParam("value")){
                        var whichOne = event.getSource();
                        $A.util.addClass(whichOne, "tier-one");
                    }
                }
                var total = (lineItems.productlineitems[temp].product.Product_MRP__c) * (lineItems.productlineitems[temp].productLineItem.Quantity__c);
                totalBefore = totalBefore + total;
                var discount =  (lineItems.productlineitems[temp].productLineItem.Overall_Percentage__c/100) ;
                var discountamount = total * discount;
                totalDiscount = totalDiscount + discountamount;
                var final = total - discountamount;
                totalAfter = totalAfter + final;
                lineItems.productlineitems[temp].productLineItem.Final_Price__c = final;
                //grades.add(lineItems.productlineitems[temp].productLineItem.Grade__c);Product_Name__c
                //alert(lineItems.productlineitems[temp].product.Product_MRP__c);
                lineItems.productlineitems[temp].productLineItem.MRP__c = lineItems.productlineitems[temp].product.Product_MRP__c ;    
                lineItems.productlineitems[temp].productLineItem.Product_Code__c = lineItems.productlineitems[temp].product.ProductCode ; 
                //alert(lineItems.productlineitems[temp].product.Product_Series__c);
                lineItems.productlineitems[temp].productLineItem.Product_Name__c = lineItems.productlineitems[temp].product.Product_Series__c ; 
                lineItems.productlineitems[temp].productLineItem.Product__c = lineItems.productlineitems[temp].product.Id ;    
                grades.push(lineItems.productlineitems[temp].productLineItem.Grade__c);
            }
        }
        
      /*  var rowId=event.getSource().get("v.name");
       // alert(rowId);
        
            
            
        }*/
        var total = component.get("v.totalVal");
        total = {'totalBefore' : totalBefore, 
                 'totalDiscount' : totalDiscount , 
                 'totalAfter' : totalAfter}; 
        component.set("v.totalVal",total);
        var action = component.get("c.serviceLineItemAdd");
        action.setParams({
            "grades" : JSON.stringify(grades),
            "serviceLine" : component.get("v.serviceLineItem"),
            "spoRecordId": spo.Id
        });
        action.setCallback(this, function(response) {
            component.set("v.serviceLineItem",response.getReturnValue());
        });
        $A.enqueueAction(action);
        
        component.set("v.LineItems",lineItems);
        component.set("v.spo",component.get("v.spo"));
    },
    
    updateStatus : function(component, event, helper) {
        var lineItems = component.get("v.LineItems");
        var cancel = new Map([["Cancel",0],["Cancel With Return",0],["Blank",0]]); 
        var totalSize;
        for(var temp in lineItems.productlineitems){
            var stat = lineItems.productlineitems[temp].productLineItem.Cancellation__c;
            var count = cancel.get(stat);
            count = parseInt(count) + 1;
            cancel.set(stat,count);
            //alert('Before');
            if(stat == null || stat == ''){
                var count1 = cancel.get("Blank");
                count1 = parseInt(count1) + 1;
                cancel.set("Blank",count1);
                //alert(count1);
            }
            totalSize = parseInt(temp)+1;
        }
        var spo = component.get("v.spo");
        if(cancel.get("Cancel") > 0){
            if(cancel.get("Cancel") == totalSize){
                spo.Product_Status__c = 'Full Cancellation';
            }
            else{
                spo.Product_Status__c = 'Partial Cancellation';
            }
        }
        if(cancel.get("Cancel With Return") > 0){
            if(cancel.get("Cancel With Return") == totalSize){
                spo.Product_Status__c = 'Full Cancellation With Return';
            }
            else{
                spo.Product_Status__c = 'Partial Cancellation With Return';
            }
        }
        if(cancel.get("Cancel")==0 &&cancel.get("Cancel With Return")==0)
        {
            //alert('Inside 1');
            if(cancel.get("Blank")==totalSize)
            {
               // alert('Inside 2');
                spo.Product_Status__c ='';
            }
        }
        //if(cancel.get('') || cancel.get(undefined) || cancel.get(null)){
           //spo.Product_Status__c ='test';
           //}
        component.set("v.spo",spo);
    },
    
    addProductLI : function(component, event, helper) {
        helper.addProduct(component, event, helper);
    },
    
    removeRow : function(component, event, helper){
       var rowId = event.target.getAttribute("id");
       // alert(rowId);
       var actionLI = component.get("c.deleteProductLineItem"); 
        actionLI.setParams({
             rowIds:rowId,
            "LineItem" : JSON.stringify(component.get("v.LineItems"))
                }); 
         actionLI.setCallback(this, function(response1) {
                    if(response1.getState() == 'SUCCESS'){
                        component.set("v.LineItems",response1.getReturnValue());
                        $A.enqueueAction(component.get('c.updateLineItems'));
                    }
                });
                $A.enqueueAction(actionLI);
    },
    
    saveSPO : function(component, event, helper) {
        
        var spo = component.get("v.spo");
        var noOfOriginal=component.get("v.noOfOriginalOrder");//for original
        var Valid = component.find('req').reduce(function (validSoFar, inputCmp) {
            inputCmp.showHelpMessageIfInvalid();
            return validSoFar && !inputCmp.get('v.validity').valueMissing;
        }, true);
        
        var error;
         if(spo.SPO_Type__c=='Original Order' && noOfOriginal==1){
            alert('Only one SPO with Orginal Order'); 
            error = true;
        }
        if(!spo.Delivery_City__c ||!spo.Delivery_Street__c ||!spo.Delivery_State__c ||!spo.Delivery_Pin_Code__c ||!spo.Delivery_Country__c ){
            alert('Please enter delivery address');
            error = true;
        }
         if(!spo.Billing_Street__c ||!spo.Billing_State__c ||!spo.Billing_Pin_Code__c ||!spo.Billing_Country__c ||!spo.Billing_City__c ){
            alert('Please enter Billing address');
             error = true;
        }
        if(spo.Service_Phone_Number__c.length != 10){
            alert('Please enter 10 digit in service phone number');
            error = true;
        }
        if(spo.Signatory_Phone_Number__c.length != 10){
            alert('Please enter 10 digit in signatory phone number');
            error = true;
        }
        var field1 = component.find("HardCopy");
        if(!field1.get("v.validity").valid && !spo.Id) {
            error = true;
            field1.showHelpMessageIfInvalid();
            alert('Upload Hard Copy');
        }
        if(!error)
        if (!Valid) {
            alert('Please enter values for all required fields');
        } else {
            var spo = component.get("v.spo");
            var total = component.get("v.totalVal");
            //alert(component.get("v.schemeCodeSelectedValues"));
            spo.Scheme_Code__c =  component.get("v.schemeCodeSelectedValues");
            spo.Total_DV_Before_Discount__c = total.totalBefore;
            spo.Total_Discount_Value__c = total.totalDiscount;
            spo.Total_DV_after_Discount__c = total.totalAfter;
            var action = component.get("c.saveRecords");
            var save= event.getSource().getLocalId()
            action.setParams({
                "spoRecord" : spo,
                "LineItem" : JSON.stringify(component.get("v.LineItems")),
                "serviceLineItem":component.get("v.serviceLineItem"),
                "dealid":component.get("v.recordId")
            });
            action.setCallback(this, function(response) {
                var state = response.getState();
                if (state === "SUCCESS") {
                    component.set("v.parentId",response.getReturnValue());
                    helper.doSave(component, event, helper,'HardCopy');
                    helper.doSave(component, event, helper,'Approval');
                    helper.doSave(component, event, helper,'financeApproval');
                    helper.doSave(component, event, helper,'exceptionApproval');
                    if(response.getReturnValue() != 'undefined'){
                        var navEvt = $A.get("e.force:navigateToSObject");
                        navEvt.setParams({
                            "recordId": response.getReturnValue(),
                            "slideDevName": "Detail"
                        });
                        navEvt.fire();
                    }
                    else{
                        alert('SPO Number Already Exists'+ response.getReturnValue());
                    }
                }
                else if (state === "INCOMPLETE") {
                    alert("From server: " + response.getReturnValue());
                } else if (state === "ERROR") {
                    var errors = response.getError();
                    alert('SPO Number Already Exists'+ response.getReturnValue());
                    if (errors) {
                        if (errors[0] && errors[0].message) {
                            alert("Error message: " + errors[0].message);
                            console.log("Error message: " + errors[0].message);
                        }
                    } else {
                        alert("Unknown error"+ response.getReturnValue());
                        console.log("Unknown error");
                    }
                }
            });
            $A.enqueueAction(action);
        }
    },
    cancel:function(component, event, helper) {
        var navEvt = $A.get("e.force:navigateToSObject");
        navEvt.setParams({
            "recordId": component.get("v.recordId"),
            "slideDevName": "Detail"
        });
        navEvt.fire();
    },
    onChangeClick:function(component, event, helper) {
        component.set("v.buttonClicked",event.getSource().getLocalId());
        var buttonClicked = component.get("v.buttonClicked");
        component.set("v.isModalOpen",true);
        component.set("v."+buttonClicked,true);
    },
    closeModel: function(component, event, helper) {
        component.set("v.isModalOpen", false);
        var buttonClicked = component.get("v.buttonClicked");
        component.set("v."+buttonClicked,false);
    },
    
     onControllerFieldChange: function(component, event, helper) {     
        var controllerValueKey = event.getSource().get("v.value"); // get selected controller field value
        var depnedentFieldMap = component.get("v.depnedentFieldMap");
        
        if (controllerValueKey != '--- None ---') {
            var ListOfDependentFields = depnedentFieldMap[controllerValueKey];
            
            if(ListOfDependentFields.length > 0){
                component.set("v.bDisabledDependentFld" , false);  
                helper.fetchDepValues(component, ListOfDependentFields);    
            }else{
                component.set("v.bDisabledDependentFld" , true); 
                component.set("v.listDependingValues", ['--- None ---']);
            }  
            
        } else {
            component.set("v.listDependingValues", ['--- None ---']);
            component.set("v.bDisabledDependentFld" , true);
        }
    },
    
    
    
    
    submitDetails: function(component, event, helper) {
        var buttonClicked = component.get("v.buttonClicked");
        var action = component.get("c.updateAccount");
        var selected = component.get("v.selectedLookUpRecord");
        action.setParams({
            "spoRecord" : component.get("v.spo"),
            "buttonClicked" : buttonClicked,
            "selectedLookUpRecord" : selected.Id,
            "accaddress" :component.get('v.adddata')
            
        });
        action.setCallback(this, function(response) {
            var state = response.getState();
            if (state === "SUCCESS") {
                component.set("v.spo", response.getReturnValue());
                component.set("v.isModalOpen", false);
                component.set("v."+buttonClicked,false);
            }
            else if (state === "INCOMPLETE") {
                alert("From server: " + response.getReturnValue());
            } else if (state === "ERROR") {
                var errors = response.getError();
                if(buttonClicked == 'accountCode'){
                    alert('Please select CP account');
                }
                else{
                    alert('Please enter valid data');
                }
                if (errors) {
                    if (errors[0] && errors[0].message) {
                        alert("Error message: " + errors[0].message);
                        console.log("Error message: " + errors[0].message);
                    }
                } else {
                    alert("Unknown error");
                    console.log("Unknown error");
                }
            }
            
        });
        $A.enqueueAction(action);
    }
})
===========================
helper===
======================
({
    getPicklistValue : function(component, event, helper,sObjectName,fieldName,listVariable) {
        var action = component.get("c.getPickListValuesIntoList");
        action.setParams({
            "sObjectName": sObjectName,
            "fieldName": fieldName
        }); 
       
        action.setCallback(this, function(response) {
            if(response.getState() == 'SUCCESS'){
                //debugger;
                if(listVariable != 'schemeCodePicklistVal'){
                  
                    component.set("v."+listVariable,response.getReturnValue());
                }
                else{
                    var result = response.getReturnValue();
                    var plValues = [];
                    for (var i = 0; i < result.length; i++) {
                        plValues.push({
                            label: result[i],
                            value: result[i]
                        });
                    }
                    component.set("v."+listVariable, plValues);
                }
                if(listVariable == 'serviceCodePicklistVal'){
                  
                    this.getRecord(component, event, helper);
                }
                
                component.set("v.spo",component.get("v.spo"));
                console.log(component.get("v."+listVariable));
            }
        });
        $A.enqueueAction(action);
    },
     getStatus:function(component, event, helper){
        
        console.log('deal id is'+component.get("v.recordId"));
        var id=component.get("v.recordId");
        var action = component.get("c.getStatus");
        action.setParams({
            "recordid":id
        }); 
        action.setCallback(this, function(response) {
            if(response.getState() == 'SUCCESS'){
                var StoreResponse = response.getReturnValue();
                component.set("v.noOfOriginalOrder",StoreResponse);
            }  
        });
        $A.enqueueAction(action);
    },
    
    getRecord:function(component, event, helper){
       
        var action = component.get("c.getRecord");
        action.setParams({
            "recordId": component.get("v.recordId"),
            "newSPO":component.get("v.newSPO")
        }); 
        action.setCallback(this, function(response) {
            if(response.getState() == 'SUCCESS'){
                var data = response.getReturnValue();
                var spo='';
                var acc=[];
                for(var i in data){
                   spo=data[i].spodata;
                    acc=data[i].accaddresslist;
                }
                var industryMap = [];
                for(var key in acc){
                    industryMap.push({label: acc[key], value: acc[key]});
                }
                
                    
                component.set('v.accountMap',industryMap);
                
                if(spo.Scheme_Code__c){
                    var scheme = String(spo.Scheme_Code__c);
                    var items = [];
                    items = scheme.split(';');
                    component.set('v.schemeCodeSelectedValues',items);
                }
                component.set("v.spo",spo);
                //Line Item query
                var actionLI = component.get("c.LineItems");
                actionLI.setParams({
                    "spoRecordId": spo.Id,
                    "state": spo.Billing_State__c,
                    "dealSource":spo.Deal_Source__c,
                    "discountVal" : spo.CP_Discount__c
                }); 
                actionLI.setCallback(this, function(response1) {
                    if(response1.getState() == 'SUCCESS'){
                        component.set("v.LineItems",response1.getReturnValue());
                        
                        //this.updateLineItems(component, event, helper);
                        $A.enqueueAction(component.get('c.updateLineItems'));
                        $A.enqueueAction(component.get('c.disableSection'));
                    }
                });
                $A.enqueueAction(actionLI);
                //var test = response.getReturnValue();
                //component.set("v.spo",response.getReturnValue());
            }
        });
        $A.enqueueAction(action);
    },
    
    addProduct : function(component, event, helper) {
        var spo = component.get("v.spo");
        var ldiscount;
        if(spo.Deal_Source__c == null || spo.Deal_Source__c == undefined)
            ldiscount = 0;
        else if(spo.Deal_Source__c == 'CP')
            ldiscount = spo.CP_Discount__c;
            else
                ldiscount = spo.School_Discount__c;
        
        var actionLI = component.get("c.addProductLineItem");
        actionLI.setParams({
            "LineItem" : JSON.stringify(component.get("v.LineItems")),
            "dealSource": spo.Deal_Source__c,
            "discountVal" : ldiscount
        }); 
        actionLI.setCallback(this, function(response1) {
            if(response1.getState() == 'SUCCESS'){
                component.set("v.LineItems",response1.getReturnValue());
                $A.enqueueAction(component.get('c.updateLineItems'));
            }
        });
        $A.enqueueAction(actionLI);
    },
    fetchPicklistValues: function(component,objDetails,controllerField, dependentField) {
        // call the server side function  
        var action = component.get("c.getDependentMap");
        // pass paramerters [object definition , contrller field name ,dependent field name] -
        // to server side function 
        action.setParams({
            'objDetail' : objDetails,
            'contrfieldApiName': controllerField,
            'depfieldApiName': dependentField 
        });
        //set callback   
        action.setCallback(this, function(response) {
            if (response.getState() == "SUCCESS") {
                //store the return response from server (map<string,List<string>>)  
                var StoreResponse = response.getReturnValue();
                
                // once set #StoreResponse to depnedentFieldMap attribute 
                component.set("v.depnedentFieldMap",StoreResponse);
                
                // create a empty array for store map keys(@@--->which is controller picklist values) 
                var listOfkeys = []; // for store all map keys (controller picklist values)
                var ControllerField = []; // for store controller picklist value to set on lightning:select. 
                
                // play a for loop on Return map 
                // and fill the all map key on listOfkeys variable.
                for (var singlekey in StoreResponse) {
                    listOfkeys.push(singlekey);
                }
                
                //set the controller field value for lightning:select
                if (listOfkeys != undefined && listOfkeys.length > 0) {
                    ControllerField.push('--- None ---');
                }
                
                for (var i = 0; i < listOfkeys.length; i++) {
                    ControllerField.push(listOfkeys[i]);
                }  
                // set the ControllerField variable values to country(controller picklist field)
                component.set("v.listControllingValues", ControllerField);
            }else{
                alert('Something went wrong. Please contact your system administrator');
            }
        });
        $A.enqueueAction(action);
    },
    
    fetchDepValues: function(component, ListOfDependentFields) {
        // create a empty array var for store dependent picklist values for controller field  
        var dependentFields = [];
        dependentFields.push('--- None ---');
        for (var i = 0; i < ListOfDependentFields.length; i++) {
            dependentFields.push(ListOfDependentFields[i]);
        }
        // set the dependentFields variable values to store(dependent picklist field) on lightning:select
        component.set("v.listDependingValues", dependentFields);
        
    },
    
    doSave: function(component, event, helper,fileID) {
        //var name = event.getSource().getLocalId();
        if (component.find(fileID).get("v.files") != null){
            if (component.find(fileID).get("v.files").length > 0) {
                var file = component.find(fileID).get("v.files");
                this.uploadHelper(component, event, file,fileID);
            } else {
                alert('Please Select a Valid File');
            }
        }
    },
    
    MAX_FILE_SIZE: 7864320, //Max file size 7.5 MB 
    CHUNK_SIZE: 750000,      
    
    uploadHelper: function(component, event, file, fileID) {
        //component.set("v.showLoadingSpinner", true);
        var fileInput = file;
        var file = fileInput[0];
        var self = this; 
        if (file.size > self.MAX_FILE_SIZE) {
            //component.set("v.showLoadingSpinner", false);
            component.set("v.fileName", 'Alert : File size cannot exceed ' + self.MAX_FILE_SIZE + ' bytes.\n' + ' Selected file size: ' + file.size);
            return;
        }
        
        var objFileReader = new FileReader();
        objFileReader.onload = $A.getCallback(function() {
            var fileContents = objFileReader.result;
            var base64 = 'base64,';
            var dataStart = fileContents.indexOf(base64) + base64.length;
            
            fileContents = fileContents.substring(dataStart);
            self.uploadProcess(component, file, fileContents, fileID);
        });
        
        objFileReader.readAsDataURL(file);
    },
    
    uploadProcess: function(component, file, fileContents, fileID) {
        var startPosition = 0;
        var endPosition = Math.min(fileContents.length, startPosition + this.CHUNK_SIZE);
        
        this.uploadInChunk(component, file, fileContents, startPosition, endPosition, '' ,fileID);
    },
    
    
    uploadInChunk: function(component, file, fileContents, startPosition, endPosition, attachId ,fileID) {
        var getchunk = fileContents.substring(startPosition, endPosition);
        var action = component.get("c.saveChunk");
        action.setParams({
            parentId: component.get("v.parentId"),
            fileName: fileID +' '+ file.name,
            base64Data: encodeURIComponent(getchunk),
            contentType: file.type,
            fileId: attachId
        });
        
        action.setCallback(this, function(response) {
            attachId = response.getReturnValue();
            var state = response.getState();
            if (state === "SUCCESS") {
                startPosition = endPosition;
                endPosition = Math.min(fileContents.length, startPosition + this.CHUNK_SIZE);
                if (startPosition < endPosition) {
                    this.uploadInChunk(component, file, fileContents, startPosition, endPosition, attachId);
                } else {
                    //alert('your File is uploaded successfully');
                    //component.set("v.showLoadingSpinner", false);
                }
            } else if (state === "INCOMPLETE") {
                alert("From server: " + response.getReturnValue());
            } else if (state === "ERROR") {
                var errors = response.getError();
                if (errors) {
                    if (errors[0] && errors[0].message) {
                        console.log("Error message: " + errors[0].message);
                    }
                } else {
                    console.log("Unknown error");
                }
            }
        });
        $A.enqueueAction(action);
    },
    getaccount: function(component, event,helper) {
         var action = component.get("c.getaccount");
          action.setParams({
              'Dealid':component.get('v.recordId')
        });
        //set callback   
        action.setCallback(this, function(response) {
            if (response.getState() == "SUCCESS") {
                //store the return response from server (map<string,List<string>>)  
               
            }
        });
          $A.enqueueAction(action);
        
    }
})
========================
style====
=========================
.THIS form{
    width: 100%;
}
.THIS .tier-one {
  background-color: red;
}
================================

SVG
============
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<svg width="120px" height="120px" viewBox="0 0 120 120" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
	<g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
		<path d="M120,108 C120,114.6 114.6,120 108,120 L12,120 C5.4,120 0,114.6 0,108 L0,12 C0,5.4 5.4,0 12,0 L108,0 C114.6,0 120,5.4 120,12 L120,108 L120,108 Z" id="Shape" fill="#2A739E"/>
		<path d="M77.7383308,20 L61.1640113,20 L44.7300055,63.2000173 L56.0543288,63.2000173 L40,99.623291 L72.7458388,54.5871812 L60.907727,54.5871812 L77.7383308,20 Z" id="Path-1" fill="#FFFFFF"/>
	</g>
</svg>

==================================

SPO Module Controller===
=====================================
public with sharing class SPOModuleController {
    
    @AuraEnabled(cacheable=true)
    Public static list<wrapper> getRecord(id recordId){
        list<wrapper> wraplist=new list<wrapper>();
        String DealPrefix = Deal__c.SObjectType.getDescribe().getKeyPrefix();
        String DealPrefix1 = SPO__c.SObjectType.getDescribe().getKeyPrefix();
        system.debug('DealPrefix==' + DealPrefix);
        system.debug('DealPrefix==' + DealPrefix1);
        if(string.valueOf(recordId).startsWith(DealPrefix)){
            Deal__c objectDeal = [select id,Account__c,Account__r.Name,Account__r.School_Code__c,Account__r.Billing_Street__c,Account__r.Billing_State__c,Academic_Year__c,School_Code__c,
                                  Account__r.Billing_Country__c,Account__r.Billing_City__c,Account__r.Billing_Postal_Code__c,Account__r.Other_City_1__c,Account__r.Other_City_2__c,
                                  Account__r.Other_City_3__c,Account__r.Other_Country_2__c,Account__r.Other_Country_1__c,Account__r.Other_Country_3__c,Account__r.Other_Postal_code_2__c,
                                  Account__r.Other_Postal_Code_1__c,Account__r.Other_Postal_Code_3__c,Account__r.Other_State_2__c,Account__r.Other_State_1__c,Account__r.Other_State_3__c,
                                  Account__r.other_Street_1__c,Account__r.Signatory_Phone_Number__c,Account__r.Signatory_Email__c,Account__r.Service_Phone_Number__c,Account__r.Service_Email__c,Account__r.Other_Street_2__c,Account__r.Other_Street_3__c,
                                  Account__r.Shipping_City__c,Account__r.Shipping_Country__c,Account__r.Shipping_Postal_Code__c,
                                  Channel_Partner_Name__r.Signatory_Phone_Number__c,Channel_Partner_Name__r.Signatory_Email__c,Channel_Partner_Name__r.Service_Phone_Number__c,Channel_Partner_Name__r.Service_Email__c,
                                  Account__r.Shipping_State__c,Account__r.CP_discount_percentage__c,Account__r.Shipping_Street__c,Deal_Source__c,Discount_offered__c,Channel_Partner_Name__c,
                                  Channel_Partner_Name__r.Billing_Street__c,Channel_Partner_Name__r.Billing_State__c,Channel_Partner_Name__r.CP_discount_percentage__c,
                                  Channel_Partner_Name__r.Name,Channel_Partner_Name__r.School_Code__c,Channel_Partner_Name__r.Channel_Partner_Code__c,Account__r.Channel_Partner_Code__c,
                                  Channel_Partner_Name__r.Billing_Country__c,Channel_Partner_Name__r.Billing_City__c,Channel_Partner_Name__r.Billing_Postal_Code__c,
                                  (SELECT Academic_Year__c,Account__c,Advance_Amount__c,Advance_Collected_From__c,Advance_Date__c,Advance_Mode__c,
                                   Advance_Reference_Number__c,Billing_City__c,	Billing_Country__c,Billing_Pin_Code__c,Billing_State__c,Billing_Street__c,
                                   Client_Confirmation_Status__c,CP_Discount__c,CreatedById,CreatedDate,Deal__c,
                                   Delivery_City__c,Delivery_Pin_Code__c,Delivery_State__c,	Delivery_Country__c,Delivery_Street__c,Expected_Date_of_Delivery__c,
                                   FIN_Verified_Reject_Attachment__c,FIN_Verified_Reject_Finance__c,FIN_Verified_Reject_Other_Finance__c,
                                   FIN_Verified_Reject_Payment__c,Id,IsDeleted,LastActivityDate,LastModifiedById,LastModifiedDate,
                                   Name,OPS_Verified_Reject_Billing__c,OPS_Verified_Reject_Delivery__c,
                                   OPS_Verified_Reject_Discount__c,OPS_Verified_Reject_Finance__c,OPS_Verified_Reject_Other_Finance__c,
                                   OPS_Verified_Reject_Other_Service__c,OPS_Verified_Reject_SPO_Metadata__c,Order_Sign_Date__c,OwnerId,
                                   Payment_Note_10__c,Payment_Note_50__c,Payment_Note_70__c,Payment_Note_75__c,Payment_Note_100__c,Scheme_Code__c,
                                   School_Discount__c,Service_Email_ID__c,Service_Phone_Number__c,Signatory_Email_ID__c,Signatory_Phone_Number__c,
                                   SPO_Number__c,SPO_Type__c,Status__c,OPs_Approved_date__c,Finance_Approved_Date__c,SystemModstamp,OTP_Entered__c,OTP_Sent__c,Service_Name__c,Signatory_Name__c,
                                   Billing_Account_Name__c,Billing_Account__c,Delivery_To__c,Delivery_Account__c,Delivery_Account_Name__c,Billing_Account_Code__c,Delivery_Account_Code__c from SPOs__r) 
                                  from Deal__c where Id =: recordId limit 1];
            /* if(objectDeal.SPOs__r.size()>0  && !newSPO){
return objectDeal.SPOs__r[0];
}
else{*/ 
            
            Account ac=[SELECT Id, IsDeleted, MasterRecordId, Name, Type, RecordTypeId, BillingCity, BillingStreet, BillingState,
                        BillingLongitude, BillingPostalCode, BillingCountry, BillingLatitude, BillingGeocodeAccuracy, BillingAddress, 
                        ShippingStreet, ShippingCity, ShippingPostalCode, ShippingState, ShippingCountry, ShippingLatitude, ShippingLongitude, 
                        ShippingAddress, Shipping_Country__c, Shipping_City__c, Shipping_State__c, Shipping_Postal_Code__c, Shipping_Street__c, 
                        State__c, Street__c, Referred_By__c, Postal_Code1__c, Other_City_1__c, Other_City_2__c, Other_City_3__c,
                        Other_Country_1__c, Other_Country_2__c, Other_Country_3__c, Other_Postal_Code_1__c, Other_Postal_Code_3__c,
                        Other_Postal_code_2__c, Other_State_1__c, Other_State_2__c, Other_State_3__c, Other_Street_2__c, Other_Street_3__c,
                        other_Street_1__c, Billing_Street__c, Billing_State__c, Country__c, City__c, Signatory_Phone_Number__c, Billing_City__c,
                        Billing_Country__c, Billing_Postal_Code__c FROM Account where id=:objectDeal.Account__c];
            SPO__c spoRecord = new SPO__c( Account__c  = objectDeal.Account__c,
                                          School_Discount__c=0,
                                          Academic_Year__c = objectDeal.Academic_Year__c,
                                          Deal__c=objectDeal.id
                                         );
            
            if(objectDeal.Account__r.other_Street_3__c!=null){
                spoRecord.Delivery_Street__c=objectDeal.Account__r.Other_Street_3__c;
                spoRecord.Delivery_State__c=objectDeal.Account__r.Other_State_3__c;
                spoRecord.Delivery_Pin_Code__c=objectDeal.Account__r.Other_Postal_Code_3__c;
                spoRecord.Delivery_City__c=objectDeal.Account__r.Other_City_3__c;
                spoRecord.Delivery_Country__c=objectDeal.Account__r.Other_Country_3__c;
                //spoRecord.Service_Name__c=objectDeal.Account__r.Name;
                spoRecord.Delivery_Account__c = objectDeal.Account__c;
                spoRecord.Delivery_Account_Name__c=objectDeal.Account__r.Name;
                spoRecord.Service_Phone_Number__c = String.valueof(objectDeal.Account__r.Service_Phone_Number__c); 
                spoRecord.Service_Email_ID__c = objectDeal.Account__r.Service_Email__c;
            }
            else if(objectDeal.Account__r.Other_Street_2__c!=null){
                spoRecord.Delivery_Street__c=objectDeal.Account__r.Other_Street_2__c;
                spoRecord.Delivery_State__c=objectDeal.Account__r.Other_State_2__c;
                spoRecord.Delivery_Pin_Code__c=objectDeal.Account__r.Other_Postal_code_2__c;
                spoRecord.Delivery_City__c=objectDeal.Account__r.Other_City_2__c;
                spoRecord.Delivery_Country__c=objectDeal.Account__r.Other_Country_2__c;
                //spoRecord.Service_Name__c=objectDeal.Account__r.Name;
                spoRecord.Delivery_Account__c = objectDeal.Account__c;
                spoRecord.Delivery_Account_Name__c=objectDeal.Account__r.Name;
                spoRecord.Service_Phone_Number__c = String.valueof(objectDeal.Account__r.Service_Phone_Number__c);
                spoRecord.Service_Email_ID__c = objectDeal.Account__r.Service_Email__c;
            }
            else if(objectDeal.Account__r.other_Street_1__c!=null){
                spoRecord.Delivery_Street__c=objectDeal.Account__r.other_Street_1__c;
                spoRecord.Delivery_State__c=objectDeal.Account__r.Other_State_1__c;
                spoRecord.Delivery_Pin_Code__c=objectDeal.Account__r.Other_Postal_Code_1__c;
                spoRecord.Delivery_City__c=objectDeal.Account__r.Other_City_1__c;
                spoRecord.Delivery_Country__c=objectDeal.Account__r.Other_Country_1__c;
                //spoRecord.Service_Name__c=objectDeal.Account__r.Name;
                spoRecord.Delivery_Account__c = objectDeal.Account__c;
                spoRecord.Delivery_Account_Name__c=objectDeal.Account__r.Name;
               spoRecord.Service_Phone_Number__c = objectDeal.Account__r.Service_Phone_Number__c;
                spoRecord.Service_Email_ID__c = objectDeal.Account__r.Service_Email__c;
            }
            else{
                spoRecord.Delivery_Street__c=objectDeal.Account__r.Shipping_Street__c;
                spoRecord.Delivery_State__c=objectDeal.Account__r.Shipping_State__c;
                spoRecord.Delivery_Pin_Code__c=objectDeal.Account__r.Shipping_Postal_Code__c;
                spoRecord.Delivery_City__c=objectDeal.Account__r.Shipping_City__c;
                spoRecord.Delivery_Country__c=objectDeal.Account__r.Shipping_Country__c;
               // spoRecord.Service_Name__c=objectDeal.Account__r.Name;
                spoRecord.Delivery_Account__c = objectDeal.Account__c;
                spoRecord.Delivery_Account_Name__c=objectDeal.Account__r.Name;
                spoRecord.Service_Phone_Number__c = objectDeal.Account__r.Service_Phone_Number__c;
                spoRecord.Service_Email_ID__c = objectDeal.Account__r.Service_Email__c;
                
            }
            if(objectDeal.Account__r.School_Code__c != null && objectDeal.Account__r.School_Code__c != ''){
                spoRecord.School_Code__c = objectDeal.Account__r.School_Code__c; 
                //spoRecord.Billing_Account_Code__c=objectDeal.Account__r.School_Code__c;
                spoRecord.Delivery_Account_Code__c=objectDeal.Account__r.School_Code__c;
            }else{
                spoRecord.Billing_Account_Code__c=objectDeal.Account__r.Channel_Partner_Code__c;
                spoRecord.Delivery_Account_Code__c = objectDeal.Account__r.Channel_Partner_Code__c;
            }
            
            if(objectDeal.Deal_Source__c=='Direct' || objectDeal.Deal_Source__c=='' || objectDeal.Deal_Source__c== null){
                spoRecord.Billing_Street__c=objectDeal.Account__r.Billing_Street__c;
                spoRecord.Billing_City__c=objectDeal.Account__r.Billing_City__c;
                spoRecord.Billing_Country__c=objectDeal.Account__r.Billing_Country__c;
                spoRecord.Billing_Pin_Code__c=objectDeal.Account__r.Billing_Postal_Code__c;
                spoRecord.Billing_State__c=objectDeal.Account__r.Billing_State__c;
                
            }
            If(objectDeal.Deal_Source__c=='CP'){
                spoRecord.Billing_Street__c=objectDeal.Channel_Partner_Name__r.Billing_Street__c;
                spoRecord.Billing_City__c=objectDeal.Channel_Partner_Name__r.Billing_City__c;
                spoRecord.Billing_Country__c=objectDeal.Channel_Partner_Name__r.Billing_Country__c;
                spoRecord.Billing_Pin_Code__c=objectDeal.Channel_Partner_Name__r.Billing_Postal_Code__c;
                spoRecord.Billing_State__c=objectDeal.Channel_Partner_Name__r.Billing_State__c;
                spoRecord.Change_Button_BG__c = false;
                spoRecord.Deal_Source__c = objectDeal.Deal_Source__c;
                spoRecord.CP_Discount__c = objectDeal.Channel_Partner_Name__r.CP_discount_percentage__c;
                spoRecord.Signatory_Name__c=objectDeal.Channel_Partner_Name__r.Name;
                spoRecord.Signatory_Phone_Number__c = objectDeal.Channel_Partner_Name__r.Signatory_Phone_Number__c;
                spoRecord.Signatory_Email_ID__c = objectDeal.Channel_Partner_Name__r.Signatory_Email__c;
                spoRecord.Billing_Account__c=objectDeal.Channel_Partner_Name__c;
                spoRecord.Billing_Account_Name__c=objectDeal.Channel_Partner_Name__r.Name;
                spoRecord.Billing_Account_Code__c=objectDeal.Channel_Partner_Name__r.Channel_Partner_Code__c;
            }else if(objectDeal.Deal_Source__c=='Direct' ){
                spoRecord.Deal_Source__c = objectDeal.Deal_Source__c;
                spoRecord.Signatory_Name__c=objectDeal.Account__r.Name;
                spoRecord.Signatory_Phone_Number__c = objectDeal.Account__r.Signatory_Phone_Number__c;
                spoRecord.Signatory_Email_ID__c = objectDeal.Account__r.Signatory_Email__c;
                spoRecord.Billing_Account__c=objectDeal.Account__c;
                spoRecord.Billing_Account_Name__c=objectDeal.Account__r.Name;
                //spoRecord.Billing_Account_Code__c=objectDeal.Account__r.Channel_Partner_Code__c;
                spoRecord.Billing_Account_Code__c=objectDeal.Account__r.School_Code__c;
                spoRecord.Change_Button_BG__c = true;
            }
            else{
                spoRecord.Deal_Source__c = 'CP';
                spoRecord.Change_Button_BG__c = true;
                spoRecord.CP_Discount__c = objectDeal.Account__r.CP_discount_percentage__c;
                spoRecord.Signatory_Name__c=objectDeal.Account__r.Name;
                spoRecord.Signatory_Phone_Number__c = objectDeal.Account__r.Signatory_Phone_Number__c;
                spoRecord.Signatory_Email_ID__c = objectDeal.Account__r.Signatory_Email__c;
                spoRecord.Billing_Account__c=objectDeal.Account__c;
                spoRecord.Billing_Account_Name__c=objectDeal.Account__r.Name;
                spoRecord.Billing_Account_Code__c=objectDeal.Account__r.Channel_Partner_Code__c;
            }
              map<string,list<string>> accaddress=new  map<string,list<string>>();
        /*   list<string> add1=new list<string>();
            add1.add(ac.City__c);
            add1.add(ac.Country__c);
             add1.add(ac.State__c);
            add1.add(ac.Postal_Code1__c);
             add1.add(ac.Street__c);
            accaddress.put(ac.Street__c,add1);
            
               list<string> add2=new list<string>();
            add2.add(ac.Billing_City__c);
            add2.add(ac.Billing_Country__c);
             add2.add(ac.Billing_State__c);
             add2.add(ac.Billing_Postal_Code__c);  
              add2.add(ac.Billing_Street__c); 
            accaddress.put(ac.Billing_Street__c,add2);*/
            
                     list<string> add3=new list<string>();
             add3.add(ac.Shipping_Street__c);
            add3.add(ac.Shipping_City__c);
            add3.add(ac.Shipping_Country__c);
             add3.add(ac.Shipping_State__c);
             add3.add(ac.Shipping_Postal_Code__c);  
             
            
            if(ac.Shipping_Street__c!=null){
            accaddress.put(ac.Shipping_Street__c,add3);
            }
            
                 list<string> add4=new list<string>();
             add4.add(ac.other_Street_1__c);
            add4.add(ac.Other_City_1__c);
            add4.add(ac.Other_Country_1__c);
             add4.add(ac.Other_State_1__c);
             add4.add(ac.Postal_Code1__c);// change this line up and all the fields selcted in list should be same order         
          
            if(ac.other_Street_1__c!=null){
            accaddress.put(ac.other_Street_1__c ,add4);
            }
           
            
                  list<string> add5=new list<string>();
              add5.add(ac.other_Street_2__c); 
            add5.add(ac.Other_City_2__c);
            add5.add(ac.Other_Country_2__c);
             add5.add(ac.Other_State_2__c);
             add5.add(ac.Other_Postal_code_2__c);    
           
              if(ac.other_Street_2__c!=null){
             accaddress.put(ac.other_Street_2__c ,add5);
            }
           
           
            
               list<string> add6=new list<string>();
            add6.add(ac.other_Street_3__c);
            add6.add(ac.Other_City_3__c);
            add6.add(ac.Other_Country_3__c);
             add6.add(ac.Other_State_3__c);
             add6.add(ac.Other_Postal_code_3__c);
              
              
              if(ac.other_Street_3__c!=null){
          accaddress.put(ac.other_Street_3__c ,add6);
            }
            
           // return spoRecord;
           wrapper wp=new wrapper();
            wp.spodata=sporecord;
            wp.accaddresslist=accaddress;
            wraplist.add(wp);
            return wraplist;
            
        }
        // }
        else{
            
          SPO__c sp=   [SELECT Academic_Year__c,Account__c,Advance_Amount__c,Advance_Collected_From__c,
                    Advance_Date__c,Advance_Mode__c,Advance_Reference_Number__c,Billing_City__c,
                    Billing_Country__c,Billing_Pin_Code__c,Billing_State__c,Billing_Street__c,
                    Client_Confirmation_Status__c,CP_Discount__c,CreatedById,CreatedDate,Deal_Source__c,
                    Deal__c,Delivery_City__c,Delivery_Country__c,Delivery_Pin_Code__c,
                    Delivery_State__c,Delivery_Street__c,Expected_Date_of_Delivery__c,
                    FIN_Attach_Rejection_Notes__c,FIN_Finance_Rejection_Notes__c,FIN_Other_Finance_Rejection_Notes__c,
                    FIN_Payment_Rejection_Notes__c,FIN_Verified_Reject_Attachment__c,FIN_Verified_Reject_Finance__c,
                    FIN_Verified_Reject_Other_Finance__c,FIN_Verified_Reject_Payment__c,Id,IsDeleted,LastActivityDate,
                    LastModifiedById,LastModifiedDate,Name,OPS_Billing_Rejection_Notes__c,
                    OPS_Delivery_Rejection_Notes__c,OPS_Discount_Rejection_Notes__c,OPS_Finance_Rejection_Notes__c,
                    OPS_Metadata_Rejection_Notes__c,OPS_Other_Finance_Rejection_Notes__c,OPS_Other_Offering_Rejection_Notes__c,
                    OPS_Verified_Reject_Billing__c,OPS_Verified_Reject_Delivery__c,OPS_Verified_Reject_Discount__c,
                    OPS_Verified_Reject_Finance__c,OPS_Verified_Reject_Other_Finance__c,OPS_Verified_Reject_Other_Service__c,
                    OPS_Verified_Reject_SPO_Metadata__c,Order_Sign_Date__c,OwnerId,Payment_Note_10__c,Payment_Note_50__c,
                    Payment_Note_70__c,Payment_Note_75__c,Payment_Note_100__c,Scheme_Code__c,School_Code__c,School_Discount__c,
                    Service_Email_ID__c,Service_Phone_Number__c,Signatory_Email_ID__c,Signatory_Phone_Number__c,Service_Name__c,Signatory_Name__c,SPO_Number__c,
                    SPO_Type__c,Status__c,Finance_Approved_Date__c,OPs_Approved_date__c,SystemModstamp,Total_Discount_Value__c,Total_DV_After_Discount__c,
                    Total_DV_before_Discount__c,OTP_Entered__c,OTP_Sent__c,Billing_Account_Name__c,Delivery_Account_Name__c,
                    Billing_Account_Code__c,Delivery_Account_Code__c,Billing_Account__c,Delivery_Account__c,Delivery_To__c,deal__r.Account__c FROM SPO__c where id =: recordId];
  Account ac=[SELECT Id, IsDeleted, MasterRecordId, Name, Type, RecordTypeId, BillingCity, BillingStreet, BillingState,
                        BillingLongitude, BillingPostalCode, BillingCountry, BillingLatitude, BillingGeocodeAccuracy, BillingAddress, 
                        ShippingStreet, ShippingCity, ShippingPostalCode, ShippingState, ShippingCountry, ShippingLatitude, ShippingLongitude, 
                        ShippingAddress, Shipping_Country__c, Shipping_City__c, Shipping_State__c, Shipping_Postal_Code__c, Shipping_Street__c, 
                        State__c, Street__c, Referred_By__c, Postal_Code1__c, Other_City_1__c, Other_City_2__c, Other_City_3__c,
                        Other_Country_1__c, Other_Country_2__c, Other_Country_3__c, Other_Postal_Code_1__c, Other_Postal_Code_3__c,
                        Other_Postal_code_2__c, Other_State_1__c, Other_State_2__c, Other_State_3__c, Other_Street_2__c, Other_Street_3__c,
                        other_Street_1__c, Billing_Street__c, Billing_State__c, Country__c, City__c, Signatory_Phone_Number__c, Billing_City__c,
                        Billing_Country__c, Billing_Postal_Code__c FROM Account where id=:sp.deal__r.Account__c];
         map<string,list<string>> accaddress=new  map<string,list<string>>();
         /*  list<string> add1=new list<string>();
            add1.add(ac.City__c);
            add1.add(ac.Country__c);
             add1.add(ac.State__c);
            add1.add(ac.Postal_Code1__c);
            add1.add(ac.Street__c);
            accaddress.put(ac.Street__c,add1);
            
               list<string> add2=new list<string>();
            add2.add(ac.Billing_City__c);
            add2.add(ac.Billing_Country__c);
             add2.add(ac.Billing_State__c);
             add2.add(ac.Billing_Postal_Code__c);   
             add2.add(ac.Billing_Street__c);
            accaddress.put(ac.Billing_Street__c,add2);*/
            
                     list<string> add3=new list<string>();
             add3.add(ac.Shipping_Street__c);
            add3.add(ac.Shipping_City__c);
            add3.add(ac.Shipping_Country__c);
             add3.add(ac.Shipping_State__c);
             add3.add(ac.Shipping_Postal_Code__c);
              
                 if(ac.Shipping_Street__c!=null){
       accaddress.put(ac.Shipping_Street__c,add3);
            }
            
            
            
                 list<string> add4=new list<string>();
            
             add4.add(ac.other_Street_1__c); 
            add4.add(ac.Other_City_1__c);
            add4.add(ac.Other_Country_1__c);
             add4.add(ac.Other_State_1__c); 
             add4.add(ac.Other_Postal_code_1__c);
               
                  if(ac.other_Street_1__c!=null){
          accaddress.put(ac.other_Street_1__c ,add4);
            }
        
            
                  list<string> add5=new list<string>();
               add5.add(ac.other_Street_2__c);  
            add5.add(ac.Other_City_2__c);
            add5.add(ac.Other_Country_2__c);
              add5.add(ac.Other_State_2__c); 
             add5.add(ac.Other_Postal_code_2__c);
             
         
                if(ac.other_Street_2__c!=null){
  accaddress.put(ac.other_Street_2__c ,add5);
            }
          
            
               list<string> add6=new list<string>();
             add6.add(ac.Other_State_3__c);
            add6.add(ac.Other_City_3__c);
            add6.add(ac.Other_Country_3__c);
             add6.add(ac.Other_State_3__c);
            
             add6.add(ac.Other_Postal_Code_3__c);   
              
                if(ac.other_Street_3__c!=null){
     accaddress.put(ac.other_Street_3__c ,add6);
            }
         
               wrapper wp=new wrapper();
            wp.spodata=sp;
            wp.accaddresslist=accaddress;
            wraplist.add(wp);
            return wraplist;
            
        }
    }
    public class wrapper{
        @AuraEnabled
        public SPO__c spodata {get;set;}
          @AuraEnabled
        public map<string,list<string>> accaddresslist {get;set;}
        
        
    }
    
    @AuraEnabled
    Public static lineItems addProductLineItem(String LineItem, string dealSource, decimal discountVal){
        Id productRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Product').getRecordTypeId();
        List<productlineitem> listofproductLineItems = new List<productlineitem>();
        List<servicelineitem> listofserviceLineItems = new List<servicelineitem>();
        List<paymentlineitem> listofpaymentLineItems = new List<paymentlineitem>();
        lineitems LineItems = (lineitems)JSON.deserialize(LineItem, lineitems.class);
        listofserviceLineItems = LineItems.servicelineitems;
        listofpaymentLineItems = LineItems.paymentlineitems;
        listofproductLineItems = LineItems.productlineitems;
        listofproductLineItems.add(new productlineitem(new SPO_Line_Item__c(Grade__c = '', Product_Name__c='', Product_Code__c='', Product__c= null, RecordTypeId = productRecordTypeId, Final_Price__c = 0,Discount__c= 0,Overall_Percentage__c=0),new Product2()));
        lineitems wrapper = new lineitems();
        wrapper.lineitemsassign(listofproductLineItems,listofserviceLineItems,listofpaymentLineItems);
        return wrapper;
    }
    @AuraEnabled
    Public static lineItems deleteProductLineItem(Integer rowIds,String LineItem){
        Id productRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Product').getRecordTypeId();
        List<productlineitem> listofproductLineItems = new List<productlineitem>();
        List<servicelineitem> listofserviceLineItems = new List<servicelineitem>();
        List<paymentlineitem> listofpaymentLineItems = new List<paymentlineitem>();
        lineitems LineItems = (lineitems)JSON.deserialize(LineItem, lineitems.class);
        
        listofserviceLineItems = LineItems.servicelineitems;
        listofpaymentLineItems = LineItems.paymentlineitems;
        listofproductLineItems = LineItems.productlineitems;
        System.debug('Row '+rowIds);
        System.debug('LINEITEM '+listofproductLineItems[rowIds]);
        //SPO_Line_Item__c del = listofproductLineItems[rowIds].productLineItem;
        listofproductLineItems.remove(rowIds);
        //delete del;
        lineitems wrapper = new lineitems();
        wrapper.lineitemsassign(listofproductLineItems,listofserviceLineItems,listofpaymentLineItems);
        return wrapper;
        
    }
    @AuraEnabled
    Public static Integer getStatus(Id recordid){
        Integer noOfOriOrder=0;
        List<SPO__c> newList=[select id,SPO_Type__c from SPO__c where Deal__c=:recordid];
        if(!newList.IsEmpty()){
            for(SPO__c sp:newList){
                if(sp.SPO_Type__c=='Original Order'){
                    noOfOriOrder=noOfOriOrder+1;
                }
            }
        }
        return noOfOriOrder;
        
    }
    
    
    
    @AuraEnabled
    Public static lineitems LineItems(string spoRecordId, string state, string dealSource, decimal discountVal){
        Id productRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Product').getRecordTypeId();
        Id serviceRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Service').getRecordTypeId();
        Id paymentRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Payment Terms').getRecordTypeId();
        List<productlineitem> listofproductLineItems = new List<productlineitem>();
        List<servicelineitem> listofserviceLineItems = new List<servicelineitem>();
        List<paymentlineitem> listofpaymentLineItems = new List<paymentlineitem>();
        
        
        
        if(spoRecordId == null){
            SPO_Line_Item__c paymentLineItem1 = new SPO_Line_Item__c(School_Payment_Terms__c = '10%', Standard_Term__c='Upon Sign Up', Revised_Payment_Term__c = null, Notes__c = '',RecordTypeId = paymentRecordTypeId);
            SPO_Line_Item__c paymentLineItem2 = new SPO_Line_Item__c(School_Payment_Terms__c = '100%', Standard_Term__c='31 August’2020', Revised_Payment_Term__c = null, Notes__c = '',RecordTypeId = paymentRecordTypeId);
            listofpaymentLineItems.add(new paymentlineitem(paymentLineItem1));
            System.debug('error '+discountVal);
            
            if(state == 'Andhra Pradesh' || state == 'Telangana'){ 
                SPO_Line_Item__c paymentLineItem3 = new SPO_Line_Item__c(School_Payment_Terms__c = '50%', Standard_Term__c='30 days of delivery', Revised_Payment_Term__c = null, Notes__c = '',RecordTypeId = paymentRecordTypeId);
                SPO_Line_Item__c paymentLineItem5 = new SPO_Line_Item__c(School_Payment_Terms__c = '75%', Standard_Term__c='31 July’2020', Revised_Payment_Term__c = null, Notes__c = '',RecordTypeId = paymentRecordTypeId);
                listofpaymentLineItems.add(new paymentlineitem(paymentLineItem3));
                listofpaymentLineItems.add(new paymentlineitem(paymentLineItem5));
            }
            else{
                SPO_Line_Item__c paymentLineItem4 = new SPO_Line_Item__c(School_Payment_Terms__c = '70%', Standard_Term__c='30 days of delivery', Revised_Payment_Term__c = null, Notes__c = '',RecordTypeId = paymentRecordTypeId);
                listofpaymentLineItems.add(new paymentlineitem(paymentLineItem4));
            }
            listofpaymentLineItems.add(new paymentlineitem(paymentLineItem2));
            
            for(integer i=1; i<=3; i++){
                listofserviceLineItems.add(new servicelineitem(new SPO_Line_Item__c(RecordTypeId = serviceRecordTypeId)));
            }
            lineitems wrapper = new lineitems();
            wrapper.lineitemsassign(listofproductLineItems,listofserviceLineItems,listofpaymentLineItems);
            System.debug('All Line Item '+wrapper);
            return wrapper;
        }
        else{
            set<id> productIds = new set<id>();
            list<SPO_Line_Item__c> listOfItems = new List<SPO_Line_Item__c>();
            listofItems = [select Grade__c,Cancellation__c,Class_Group__c,Discount__c,Exam_Code__c,Examination_Term__c,
                           Service_Code__c,Quantity__c,Product__c,MRP__c,School_Payment_Terms__c,Overall_Percentage__c,Standard_Term__c,
                           Revised_Payment_Term__c,Notes__c,recordTypeId,SPO__c
                           from SPO_Line_Item__c where SPO__c =: spoRecordId ];
            for(SPO_Line_Item__c templineItem : listofItems){
                if(templineItem.recordTypeId ==  productRecordTypeId)
                    productIds.add(templineItem.Product__c);
                if(templineItem.recordTypeId ==  paymentRecordTypeId)                                     
                    listofpaymentLineItems.add(new paymentlineitem(templineItem));
                if(templineItem.recordTypeId ==  serviceRecordTypeId)                                    
                    listofserviceLineItems.add(new servicelineitem(templineItem));
            }
            Map<id,Product2> mapofProducts = new Map<id,Product2>([select Id, Name,Product_MRP__c,Discount__c,ProductCode,Product_Series__c from product2 where id =: productIds AND IsActive=true ]);
            for(SPO_Line_Item__c templineItem : listofItems){
                if(templineItem.recordTypeId ==  productRecordTypeId)
                    listofproductLineItems.add(new productlineitem(templineItem,mapofProducts.get(templineItem.Product__c)));
            }
            System.debug('else ID '+spoRecordId);
            System.debug('Producct Line Item '+listofproductLineItems);
            
            System.debug('service Line Item '+listofserviceLineItems);
            
            System.debug('payment Line Item '+listofpaymentLineItems);
            lineitems wrapper = new lineitems();
            wrapper.lineitemsassign(listofproductLineItems,listofserviceLineItems,listofpaymentLineItems);
            return wrapper;
        }
    }
    
    public class lineitems{
        @AuraEnabled public List<productlineitem> productlineitems;
        @AuraEnabled public List<servicelineitem> servicelineitems;
        @AuraEnabled public List<paymentlineitem> paymentlineitems;
        public void lineitemsassign(List<productlineitem> productlineitems, List<servicelineitem> servicelineitems, List<paymentlineitem> paymentlineitems){
            this.productlineitems = productlineitems;
            this.servicelineitems = servicelineitems;
            this.paymentlineitems = paymentlineitems;
        }
    }
    
    Public class productlineitem{
        @AuraEnabled public SPO_Line_Item__c productLineItem;
        @AuraEnabled public  product2 product;
        public productlineitem(SPO_Line_Item__c productLineItem, Product2 product){
            this.productLineItem = productLineItem;
            this.product = product;
            this.product.Product_Series__c = product.Product_Series__c;
        }
    }
    
    Public class servicelineitem{
        @AuraEnabled public SPO_Line_Item__c serviceLineItem;
        public servicelineitem(SPO_Line_Item__c serviceLineItem){
            this.serviceLineItem = serviceLineItem;
        }
    }
    
    Public class paymentlineitem{
        @AuraEnabled public SPO_Line_Item__c paymentLineItem;
        public paymentlineitem(SPO_Line_Item__c paymentLineItem){
            this.paymentLineItem = paymentLineItem;
        }
    }
    
    @AuraEnabled
    Public static List<SPO_Line_Item__c> serviceLineItemAdd(String grades, List<SPO_Line_Item__c> serviceLine,String spoRecordId){
        List<SPO_Line_Item__c> serviceLineItems = new List<SPO_Line_Item__c>();
        set<string> gradesSet = new set<String>();
        set<string> lineItemgrade = new set<string>();
        if(spoRecordId != null){
            List<SPO_Line_Item__c> ExserviceLineItems = new List<SPO_Line_Item__c>();
            Id serviceRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Service').getRecordTypeId();
            ExserviceLineItems = [select Grade__c,Cancellation__c,Class_Group__c,Discount__c,Exam_Code__c,Examination_Term__c,
                    Service_Code__c,Quantity__c,Product__c,Overall_Percentage__c,MRP__c,School_Payment_Terms__c,Standard_Term__c,
                    Revised_Payment_Term__c,Notes__c,recordTypeId,SPO__c
                    from SPO_Line_Item__c where SPO__c =: spoRecordId and recordTypeId =: serviceRecordTypeId];
            for(SPO_Line_Item__c tempLineItem : ExserviceLineItems){
                lineItemgrade.add(tempLineItem.Class_Group__c);
                serviceLineItems.add(tempLineItem);
                gradesSet.add(tempLineItem.Class_Group__c);
            }
        }
        if(grades != null && grades != '')
        	gradesSet.addAll(grades.remove('[').remove(']').remove('"').Split(','));
        System.debug('SEr '+serviceLine);
        if(serviceLine != null)
            for(SPO_Line_Item__c tempLineItem : serviceLine){
                if(!lineItemgrade.contains(tempLineItem.Class_Group__c)){
                    lineItemgrade.add(tempLineItem.Class_Group__c);
                    serviceLineItems.add(tempLineItem);
                    System.debug('Inseide with/t save '+ tempLineItem.Class_Group__c);
                }
            }
        System.debug('Grp '+lineItemgrade);
        if(gradesSet.contains('NUR') || gradesSet.contains('LKG') || gradesSet.contains('UKG')){
            if(!lineItemgrade.contains('NUR-UKG'))
                serviceLineItems.add(new SPO_Line_Item__c(Class_Group__c = 'NUR-UKG'));
        }
        System.debug('set '+gradesSet);
        if(gradesSet.contains('G1') || gradesSet.contains('G2') || gradesSet.contains('G3') || gradesSet.contains('G4') || gradesSet.contains('G5')){
            System.debug('Inseite '+lineItemgrade.contains('G1-G5'));
            if(!lineItemgrade.contains('G1-G5')){
                serviceLineItems.add(new SPO_Line_Item__c(Class_Group__c = 'G1-G5'));
            }
        }
        if(gradesSet.contains('G6') || gradesSet.contains('G7') || gradesSet.contains('G8') || gradesSet.contains('G9') || gradesSet.contains('G10')){
            if(!lineItemgrade.contains('G6-G10'))
                serviceLineItems.add(new SPO_Line_Item__c(Class_Group__c = 'G6-G10'));
        }
        System.debug('final '+serviceLineItems);
        return serviceLineItems;
        
    }
    
    @AuraEnabled
    Public static SPO__c sendOTPSMS(SPO__c spoRecord){
        SMSMagic__c SMS = SMSMagic__c.getValues('SPO');
        Integer OTP = Integer.valueof((Math.random() * 10000));
        spoRecord.OTP_Sent__c = OTP;
        
        Http httpInstance=new Http();
        HttpRequest httpRequestInstance=new HttpRequest();
        httpRequestInstance.setEndpoint(SMS.EndPoint__c);
        httpRequestInstance.setMethod('POST');
        httpRequestInstance.setHeader('content-type', 'application/json');
        httpRequestInstance.setHeader('apiKey', SMS.APIKey__c );
        
        
        String Content = SMS.Content__c.replace('<OTP>', String.valueof(OTP));
        Content = Content.replace('<SPONumber>', spoRecord.SPO_Number__c);
        String jsonBody = JSON.serialize(new SMSBody(Content,SMS.SenderID__c, spoRecord.Signatory_Phone_Number__c));
        
        
        httpRequestInstance.setBody(jsonBody);
        System.debug('request Body'+httpRequestInstance.getBody());
        
        //Response 
        HttpResponse httpResponseInstance;
        if(!Test.isRunningTest()){
            httpResponseInstance=httpInstance.send(httpRequestInstance);
            System.debug('Response '+httpResponseInstance.getBody());
        }
        
        return spoRecord;
    }
    
    Public class SMSBody{
        String sms_text;
        String sender_id;
        String mobile_number;
        public SMSBody(String sms_text, String sender_id, String mobile_number){
            this.sms_text = sms_text;
            this.sender_id = sender_id;
            this.mobile_number = mobile_number;
        }
    }
    
    @AuraEnabled
    Public static Map<string,Map<string,string>> dynamicCodePickListValues(){
        List<Product2> listOfProducts = new List<Product2>();
        Map<string,Map<string,string>> mapOfGradeandProduct = new Map<string,Map<string,string>>();
        listOfProducts = [select id,Name,Product_Grade__c,Product_Series__c from Product2 where IsActive= true];
        //listOfProducts = [select id,Name,Product_Grade__c,Product_Series__c from Product2 ];
        for(Product2 tempProduct : listOfProducts){
            if(mapOfGradeandProduct.keySet().contains(tempProduct.Product_Grade__c)){
                Map<string,string> tempValue = mapOfGradeandProduct.get(tempProduct.Product_Grade__c);
                tempValue.put(tempProduct.Name,tempProduct.Id);
                mapOfGradeandProduct.Put(tempProduct.Product_Grade__c,tempValue);
            }
            else{
                Map<string,string> tempValue = new Map<string,string>();
                tempValue.put(tempProduct.Name,tempProduct.Id);
                mapOfGradeandProduct.Put(tempProduct.Product_Grade__c,tempValue);
            }
        }
        return mapOfGradeandProduct;
    }
    
    @AuraEnabled
    public static string saveRecords(SPO__c spoRecord, String LineItem,List<SPO_Line_Item__c> serviceLineItem,id dealid){
        system.debug('Record '+spoRecord);
        System.debug('Line Items '+LineItem);
         SObjectType object1=dealid.getSobjectType();
        String objectStr = 'Deal__c';
          Schema.SObjectType object2 = Schema.getGlobalDescribe().get(objectStr);
        system.debug('deal'+object1);
        system.debug('deal'+object2);
        List<SPO__c> listOfSpo=[select id,SPO_Type__c from SPO__c where Deal__c=:dealid];
        system.debug('list of spo'+listOfSpo);
        string spoName;
        lineitems LineItems = (lineitems)JSON.deserialize(LineItem, lineitems.class) ;
        Id productRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Product').getRecordTypeId();
        Id serviceRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Service').getRecordTypeId();
        Id paymentRecordTypeId = Schema.SObjectType.SPO_Line_Item__c.getRecordTypeInfosByName().get('Payment Terms').getRecordTypeId();
        
        List<SPO_Line_Item__c> lineItemstoUpsert = new List<SPO_Line_Item__c>();
        string productSeries = '';
        for(productlineitem temp : LineItems.productlineitems){
            system.debug(temp.productLineItem.Product_Name__c);
            if(temp.productLineItem.Product_Name__c != null ){
                productSeries = productSeries +'-'+ temp.productLineItem.Product_Name__c.left(3);
            }
        }
        
        string schoolCode = '';
        if(spoRecord.School_Code__c != null){
            schoolCode = '-'+spoRecord.School_Code__c;   
            system.debug('school code'+schoolCode);
        }
         Integer Countnew=0;
        Integer Countadd=0;
        Integer CountVir=0;
        Integer CountOr=0;
        Integer CountOth=0;
        //if(Object1==object2){
        if(listOfSpo!=null){
        for(SPO__c spo:listOfSpo){
            if(spo.SPO_Type__c=='New'){
                Countnew=Countnew+1;
            }
            else if(spo.SPO_Type__c=='Additional'){
                Countadd=Countadd+1;
            }
            else if(spo.SPO_Type__c=='Virtual'){
                CountVir=CountVir+1;
            }
            else if(spo.SPO_Type__c=='Orginal Order'){
                CountOr=CountOr+1;
            }else{
                CountOth=CountOth+1;
            }
            }
            
        }
        //string spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c;
        if(object1==object2){
        if(spoRecord.SPO_Type__c=='New'){
         spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c + ++Countnew;
        }
        else if(spoRecord.SPO_Type__c=='Additional'){
             spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c + ++Countadd;
        }
        else if(spoRecord.SPO_Type__c=='Virtual'){
            spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c + ++CountVir;
        }else if(spoRecord.SPO_Type__c=='Original Order') {
            spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c;
        }else{
            spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c + ++CountOth;
        }
        
        System.debug(spoName);
        spoRecord.Name = spoName.left(80);
        }
        //string spoName = spoRecord.Academic_Year__c + schoolCode + '-'+spoRecord.SPO_Type__c;
       // System.debug(spoName);
        //spoRecord.Name = spoName.left(80);
        System.debug(spoRecord.Name);
        upsert spoRecord;
        System.debug('SPOId '+spoRecord.Id);
        System.debug('Line '+LineItems.productlineitems);
        for(productlineitem temp : LineItems.productlineitems){
            if(temp.productLineItem.Product__c != null){
                if(temp.productLineItem.SPO__c == null)
                    temp.productLineItem.SPO__c = spoRecord.Id;
                temp.productLineItem.Product_lookup__c = spoRecord.Id;
                lineItemstoUpsert.add(temp.productLineItem);
            }
        }
        for(SPO_Line_Item__c temp : serviceLineItem){
            if(temp.SPO__c == null)
                temp.SPO__c = spoRecord.Id;
            temp.Service__c = spoRecord.Id;
            temp.RecordTypeId = serviceRecordTypeId;
            lineItemstoUpsert.add(temp);
        }
        for(paymentlineitem temp : LineItems.paymentlineitems){
            if(temp.paymentLineItem.SPO__c == null)
                temp.paymentLineItem.SPO__c = spoRecord.Id;
            temp.paymentLineItem.Payment_Terms__c = spoRecord.Id;
            lineItemstoUpsert.add(temp.paymentLineItem);
        }
        upsert lineItemstoUpsert;
        Account object_Account = new Account();
        object_Account.id = spoRecord.Account__c;
       // object_Account.Service_Phone_Number__c = Decimal.valueof(spoRecord.Service_Phone_Number__c);
        object_Account.Service_Phone_Number__c =spoRecord.Service_Phone_Number__c;
       
        object_Account.Service_Email__c = spoRecord.Service_Email_ID__c;
        object_Account.Signatory_Phone_Number__c = spoRecord.Signatory_Phone_Number__c;
        object_Account.Service_Contact_Person_Name__c=spoRecord.Service_Name__c;
        object_Account.Signatory_Email__c = spoRecord.Signatory_Email_ID__c;
        //object_Account.Service_Contact_Person_Name__c=spoRecord.Service_Name__c;
        object_Account.Signatory_Name__c=spoRecord.Signatory_Name__c;
        update object_Account;	
       
        
       if(spoRecord.Deal_Source__c =='CP') {
         Account Object_CP=new Account();
          Object_CP.id= spoRecord.Billing_Account__c; 
          Object_CP.Signatory_Name__c=spoRecord.Signatory_Name__c ;
          Object_CP.Signatory_Phone_Number__c=spoRecord.Signatory_Phone_Number__c;
          Object_CP.Signatory_Email__c=spoRecord.Signatory_Email_ID__c;
          update Object_CP;
         
        } 
         return spoRecord.id;
    }
    
    @AuraEnabled
    public static SPO__c updateAccount(SPO__c spoRecord, string buttonClicked, String selectedLookUpRecord,string accaddress){
        system.debug('spoRecord'+spoRecord);
        system.debug('accaddress'+accaddress);
        string streetname;
       List<String> lstAlpha = new list<string>();
        if(accaddress!=null){
       lstAlpha= accaddress.split(',');
        System.debug(lstAlpha);
       
        sporecord.Delivery_Street__c=lstAlpha.get(0);
        sporecord.Delivery_City__c = lstAlpha.get(1);
        sporecord.Delivery_Country__c = lstAlpha.get(2);
         sporecord.Delivery_State__c = lstAlpha.get(3);
      sporecord.Delivery_Pin_Code__c = lstAlpha.get(4);
           
            streetname=lstAlpha.get(0);
        }
      
       // a.Postal_Code1__c = lstAlpha.get(3);
    
      
        /*system.debug('sf1==='+accaddress.get(0));
        account a = new account();
        a.name = accaddress.get(0);
        insert a;
        delete a;
        system.debug('a==' + a);
        for(string sf:accaddress){
            system.debug('sffff'+sf);
        }*/
        
            
        
        Account object_Account = new Account();
        string queryString = 'select Name,CP_discount_percentage__c,Signatory_Phone_Number__c,other_Street_1__c,Other_city_1__c,Other_State_1__c,Other_Country_1__c,Other_Postal_Code_1__c,'+
            +'other_Street_2__c,Signatory_Email__c,Other_city_2__c,Other_State_2__c,Other_Country_2__c,Other_Postal_Code_2__c,'+
            +'other_Street_3__c,Other_city_3__c,Other_State_3__c,Other_Country_3__c,Other_Postal_Code_3__c,'+
            +'Shipping_Street__c,Shipping_City__c,Shipping_State__c,Shipping_Country__c,Shipping_Postal_Code__c,'+
            +'Billing_Street__c,Billing_City__c,Billing_State__c,Billing_Country__c,Billing_Postal_Code__c,Channel_Partner_Code__c '+
            +'from Account where id = \'';
        if(buttonClicked == 'deliveryAddress'){
            queryString = queryString + spoRecord.Delivery_Account__c+'\'';
            System.debug('Q '+queryString);
            object_Account = database.query(queryString);
             if(object_Account.Shipping_Street__c != null && streetname== object_Account.Shipping_Street__c ){
                object_Account.Shipping_Street__c = spoRecord.Delivery_Street__c;
                object_Account.Shipping_City__c = spoRecord.Delivery_City__c;
                object_Account.Shipping_State__c = spoRecord.Delivery_State__c;
                object_Account.Shipping_Country__c = spoRecord.Delivery_Country__c;
                object_Account.Shipping_Postal_Code__c = spoRecord .Delivery_Pin_Code__c;
                
            }
          else if(object_Account.Shipping_Street__c == null ){
                object_Account.Shipping_Street__c = spoRecord.Delivery_Street__c;
                object_Account.Shipping_City__c = spoRecord.Delivery_City__c;
                object_Account.Shipping_State__c = spoRecord.Delivery_State__c;
                object_Account.Shipping_Country__c = spoRecord.Delivery_Country__c;
                object_Account.Shipping_Postal_Code__c = spoRecord .Delivery_Pin_Code__c;
                
            }
                else if(object_Account.other_Street_1__c != null && streetname== object_Account.other_Street_1__c ){
                object_Account.other_Street_1__c = spoRecord.Delivery_Street__c;
                object_Account.Other_city_1__c = spoRecord.Delivery_City__c;
                object_Account.Other_State_1__c = spoRecord.Delivery_State__c;
                object_Account.Other_Country_1__c = spoRecord.Delivery_Country__c;
                object_Account.Other_Postal_Code_1__c = spoRecord.Delivery_Pin_Code__c;
            }
            else if(object_Account.other_Street_1__c == null ){
                object_Account.other_Street_1__c = spoRecord.Delivery_Street__c;
                object_Account.Other_city_1__c = spoRecord.Delivery_City__c;
                object_Account.Other_State_1__c = spoRecord.Delivery_State__c;
                object_Account.Other_Country_1__c = spoRecord.Delivery_Country__c;
                object_Account.Other_Postal_Code_1__c = spoRecord.Delivery_Pin_Code__c;
            }
                else if(object_Account.other_Street_2__c != null && streetname== object_Account.other_Street_2__c){
                object_Account.other_Street_2__c = spoRecord.Delivery_Street__c;
                object_Account.Other_city_2__c = spoRecord.Delivery_City__c;
                object_Account.Other_State_2__c = spoRecord.Delivery_State__c;
                object_Account.Other_Country_2__c = spoRecord.Delivery_Country__c;
                object_Account.Other_Postal_Code_2__c = spoRecord .Delivery_Pin_Code__c;
            }
            else if(object_Account.other_Street_2__c == null){
                object_Account.other_Street_2__c = spoRecord.Delivery_Street__c;
                object_Account.Other_city_2__c = spoRecord.Delivery_City__c;
                object_Account.Other_State_2__c = spoRecord.Delivery_State__c;
                object_Account.Other_Country_2__c = spoRecord.Delivery_Country__c;
                object_Account.Other_Postal_Code_2__c = spoRecord .Delivery_Pin_Code__c;
            }
               else if(object_Account.other_Street_3__c != null && streetname== object_Account.other_Street_3__c){
                object_Account.other_Street_3__c = spoRecord.Delivery_Street__c;
                object_Account.Other_city_3__c = spoRecord.Delivery_City__c;
                object_Account.Other_State_3__c = spoRecord.Delivery_State__c;
                object_Account.Other_Country_3__c = spoRecord.Delivery_Country__c;
                object_Account.Other_Postal_Code_3__c = spoRecord .Delivery_Pin_Code__c;
            }
            else if(object_Account.other_Street_3__c == null){
                object_Account.other_Street_3__c = spoRecord.Delivery_Street__c;
                object_Account.Other_city_3__c = spoRecord.Delivery_City__c;
                object_Account.Other_State_3__c = spoRecord.Delivery_State__c;
                object_Account.Other_Country_3__c = spoRecord.Delivery_Country__c;
                object_Account.Other_Postal_Code_3__c = spoRecord .Delivery_Pin_Code__c;
            }
        }
        if(buttonClicked == 'billingAddress'){
            queryString = queryString + spoRecord.Billing_Account__c+'\'';
            object_Account = database.query(queryString);
            object_Account.Billing_Street__c = spoRecord.Billing_Street__c ;
            object_Account.Billing_City__c = spoRecord.Billing_City__c ;
            object_Account.Billing_State__c = spoRecord.Billing_State__c ;
            object_Account.Billing_Country__c = spoRecord.Billing_Country__c ;
            object_Account.Billing_Postal_Code__c = spoRecord.Billing_Pin_Code__c ;
            
        }
        if(buttonClicked == 'accountCode'){
            //selectedLookUpRecord
            
            queryString = queryString + selectedLookUpRecord+'\'';
            object_Account = database.query(queryString);
            Deal__c deal = new Deal__c();
            deal.Id = spoRecord.Deal__c;
            deal.Channel_Partner_Name__c = selectedLookUpRecord;
            Update deal;
            
            spoRecord.Billing_Street__c=object_Account.Billing_Street__c;
            spoRecord.Billing_City__c=object_Account.Billing_City__c;
            spoRecord.Billing_Country__c=object_Account.Billing_Country__c;
            spoRecord.Billing_Pin_Code__c=object_Account.Billing_Postal_Code__c;
            spoRecord.Billing_State__c=object_Account.Billing_State__c;
            spoRecord.Billing_Account__c=object_Account.id;
           // spoRecord.School_Code__c = object_Account.Channel_Partner_Code__c;
            spoRecord.Signatory_Name__c=object_Account.Name;
            spoRecord.CP_Discount__c = object_Account.CP_discount_percentage__c;
            spoRecord.Signatory_Phone_Number__c = object_Account.Signatory_Phone_Number__c;
            spoRecord.Signatory_Email_ID__c = object_Account.Signatory_Email__c;
            spoRecord.Billing_Account_Name__c=object_Account.Name;
            spoRecord.Billing_Account_Code__c=object_Account.Channel_Partner_Code__c;
        }
          lstAlpha.clear();
       accaddress='';
        update object_Account;
        return spoRecord;
    }
    
    @AuraEnabled
    public static List<String> getPickListValuesIntoList(String sObjectName,String fieldName){
        List<String> pickListValuesList = new List<String>();
        Schema.SObjectType targetType = Schema.getGlobalDescribe().get(sObjectName);
        Sobject Object_name = targetType.newSObject();
        Schema.sObjectType sobject_type = Object_name.getSObjectType(); 
        Schema.DescribeSObjectResult sobject_describe = sobject_type.getDescribe(); 
        Map<String, Schema.SObjectField> field_map = sobject_describe.fields.getMap(); 
        List<Schema.PicklistEntry> pick_list_values = field_map.get(fieldName).getDescribe().getPickListValues(); 
        pickListValuesList.add('');
        for (Schema.PicklistEntry a : pick_list_values) { 
            pickListValuesList.add(a.getValue());
        }
        return pickListValuesList;
    }
    
    @AuraEnabled
    public static Id saveChunk(Id parentId, String fileName, String base64Data, String contentType, String fileId) {
        if (fileId == '') {
            fileId = saveTheFile(parentId, fileName, base64Data, contentType);
        } else {
            appendToFile(fileId, base64Data);
        }
        return Id.valueOf(fileId);
    }
    
    public static Id saveTheFile(Id parentId, String fileName, String base64Data, String contentType) {
        base64Data = EncodingUtil.urlDecode(base64Data, 'UTF-8');
        
        Attachment oAttachment = new Attachment();
        oAttachment.parentId = parentId;
        oAttachment.Body = EncodingUtil.base64Decode(base64Data);
        oAttachment.Name = fileName;
        oAttachment.ContentType = contentType;
        
        insert oAttachment;
        
        return oAttachment.Id;
    }
    
    private static void appendToFile(Id fileId, String base64Data) {
        base64Data = EncodingUtil.urlDecode(base64Data, 'UTF-8');
        
        Attachment a = [SELECT Id, Body FROM Attachment WHERE Id =: fileId];
        
        String existingBody = EncodingUtil.base64Encode(a.Body);
        a.Body = EncodingUtil.base64Decode(existingBody + base64Data);
        update a;
    } 
     public static String base64ToBits(String validFor) {
        if (String.isEmpty(validFor)) return '';
        
        String validForBits = '';
        
        for (Integer i = 0; i < validFor.length(); i++) {
            String thisChar = validFor.mid(i, 1);
            Integer val = base64Chars.indexOf(thisChar);
            String bits = decimalToBinary(val).leftPad(6, '0');
            validForBits += bits;
        }
        
        return validForBits;
    }
    @auraEnabled
     public static Map<String, List<String>> getDependentMap(sObject objDetail, string contrfieldApiName,string depfieldApiName) {
        String controllingField = contrfieldApiName.toLowerCase();
        String dependentField = depfieldApiName.toLowerCase();
        
        Map<String,List<String>> objResults = new Map<String,List<String>>();
        
        Schema.sObjectType objType = objDetail.getSObjectType();
        if (objType==null){
            return objResults;
        }
        
        Map<String, Schema.SObjectField> objFieldMap = objType.getDescribe().fields.getMap();
        
        if (!objFieldMap.containsKey(controllingField) || !objFieldMap.containsKey(dependentField)){
            return objResults;     
        }
        
        Schema.SObjectField theField = objFieldMap.get(dependentField);
        Schema.SObjectField ctrlField = objFieldMap.get(controllingField);
        
        List<Schema.PicklistEntry> contrEntries = ctrlField.getDescribe().getPicklistValues();
        List<PicklistEntryWrapper> depEntries = wrapPicklistEntries(theField.getDescribe().getPicklistValues());
        List<String> controllingValues = new List<String>();
        
        for (Schema.PicklistEntry ple : contrEntries) {
            String label = ple.getLabel();
            objResults.put(label, new List<String>());
            controllingValues.add(label);
        }
        
        for (PicklistEntryWrapper plew : depEntries) {
            String label = plew.label;
            String validForBits = base64ToBits(plew.validFor);
            for (Integer i = 0; i < validForBits.length(); i++) {
                String bit = validForBits.mid(i, 1);
                if (bit == '1') {
                    objResults.get(controllingValues.get(i)).add(label);
                }
            }
        }
        return objResults;
    }
     public static String decimalToBinary(Integer val) {
        String bits = '';
        while (val > 0) {
            Integer remainder = Math.mod(val, 2);
            val = Integer.valueOf(Math.floor(val / 2));
            bits = String.valueOf(remainder) + bits;
        }
        return bits;
    }

    private static List<PicklistEntryWrapper> wrapPicklistEntries(List<Schema.PicklistEntry> PLEs) {
        return (List<PicklistEntryWrapper>)
            JSON.deserialize(JSON.serialize(PLEs), List<PicklistEntryWrapper>.class);
    }
     private static final String base64Chars = '' +
        'ABCDEFGHIJKLMNOPQRSTUVWXYZ' +
        'abcdefghijklmnopqrstuvwxyz' +
        '0123456789+/';
    
    public class PicklistEntryWrapper{
        public String active {get;set;}
        public String defaultValue {get;set;}
        public String label {get;set;}
        public String value {get;set;}
        public String validFor {get;set;}
        public PicklistEntryWrapper(){            
        }
    } 
  
    
}

===================================================================
=========================================================================
