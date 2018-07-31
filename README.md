# Car-detector-in-Video
MATLAB was used in this case to structure the algorithm so as to perform image , video processing 
trafficVid = VideoReader('traffic.mj2')
get(trafficVid)
implay('traffic.mj2');
figure, imshow(noDarkCar)
sedisk = strel('disk',2);
noSmallStructures = imopen(noDarkCar, sedisk);
imshow(noSmallStructures)
nframes = trafficVid.NumberOfFrames;
I = read(trafficVid, 1);
taggedCars = zeros([size(I,1) size(I,2) 3 nframes], class(I));

for k = 1 : nframes
    singleFrame = read(trafficVid, k);
     I = rgb2gray(singleFrame);
     noDarkCars = imextendedmax(I, darkCarValue); 
     noSmallStructures = imopen(noDarkCars, sedisk);
     noSmallStructures = bwareaopen(noSmallStructures, 150);
     taggedCars(:,:,:,k) = singleFrame;
     stats = regionprops(noSmallStructures, {'Centroid','Area'});
      if ~isempty([stats.Area])
        areaArray = [stats.Area];
        [junk,idx] = max(areaArray);
        c = stats(idx).Centroid;
        c = floor(fliplr(c));
        width = 2;
        row = c(1)-width:c(1)+width;
        col = c(2)-width:c(2)+width;
        taggedCars(row,col,1,k) = 255;
        taggedCars(row,col,2,k) = 0;
        taggedCars(row,col,3,k) = 0;
    end
end
frameRate = trafficVid.FrameRate;
implay(taggedCars,frameRate);

      
