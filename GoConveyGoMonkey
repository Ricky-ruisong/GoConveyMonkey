package assist

import (
	"bou.ke/monkey"
	"mypackage"
	"github.com/smartystreets/goconvey/convey"
	"github.com/stretchr/testify/assert"
	"testing"
)

//goconvey
func TestGetRtspTwinUrl(t *testing.T) {
	convey.Convey("TestGetRtspTwinUrl", t, func() {
		rtspUrl := "rtsp://xxx"         //real rtspUrl
		rtspTwinUrl := "rtsp://xxx"     //real rtspTwinUrl
		
		convey.So(getRtspTwinUrl(rtspUrl), convey.ShouldEqual, rtspTwinUrl)
	})
}

//gomonkey

func TestGetTwin(t *testing.T){
	mt1 := mypackage.MemTwin{
		Id:				"1",
		Name:			"hello",
		Description:	"the first MemTwin",
	}
	mt2 := mypackage.MemTwin{
		Id:				"2",
		Name:			"world",
		Description:	"the second MemTwin",
	}
	mr := mypackage.MemResult{Devices: []mypackage.MemTwin{mt1,mt2}}
	mypackage.SetMembership(mr)
	getTwin()
}

func TestDeleteDevicesUseless(t *testing.T){
	mt1 := mypackage.MemTwin{
		Id:				"1",
		Name:			"hello",
		Description:	"onvif-camera",
	}
	mt2 := mypackage.MemTwin{
		Id:				"2",
		Name:			"hello",
		Description:	"onvif-camera",
	}
	mr := mypackage.MemResult{Devices: []mypackage.MemTwin{mt1,mt2}}
	mypackage.SetMembership(mr)
	getTwin()
	DeviceUpdate.Store("1","hello")
	DeviceUpdate.Store("2","world2")
	DeviceUpdate.Store("3","world3")
	deleteDeviceUseless()
	i:=0
	DeviceUpdate.Range(func(key, value interface{}) bool {
		deviceId := key.(string)
		assert.Equal(t,true,mypackage.GetMemResult().Devices[i].Id== deviceId)
		i++
		return true
	})
}

func TestGetRtspUrl(t *testing.T) {
	monkey.Patch(onvif.GetRtspUrl,func(ip,port,username,password,stream string)(string,error){
		return "demo",nil
	})
	url , _ := getRtspUrl("","","","","")

	assert.Equal(t, url,"demo")
}

func TestCreateHttpFlv(t *testing.T){
	myPipline := &myPipline.myPipline{}
	monkey.Patch(getmyPipline,func(string)(*myPipline.myPipline, error){
		return myPipline,nil
	})
	monkey.Patch(createService,func(string, *myPipline.myPipline) error{
		return nil
	})
	monkey.Patch(createEndpoints,func(string , string, *myPipline.myPipline) error{
		return nil
	})
	monkey.Patch(createVirtualService,func(string,string, *myPipline.myPipline) error{
		return nil
	})
	err := createHttpFlv("127.0.0.1", "12_")
	assert.Equal(t, err,nil)
}

func TestCreateVirtualService(t *testing.T){
	pipline := myPipline.myPipline{}
	vsDevice := &istio.VirtualService{}
	monkey.Patch(virtualservice.Get,func(string, string)(*istio.VirtualService, error){
		return vsDevice,nil
	})
	monkey.Patch(virtualservice.Create,func(string,*istio.VirtualService)(error){
		return nil
	})
	err := createVirtualService("123","123",&pipline)
	assert.Equal(t, err,nil)
}

func TestGetmyPipline(t *testing.T){
	pipline := &myPipline.myPipline{}
	monkey.Patch(myPipline.Get,func(ns string, name string) (*myPipline.myPipline, error){
		return pipline,nil
	})
	res,_ := getmyPipline("1")
	assert.Equal(t, res,pipline)
}
